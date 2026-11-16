---
title: "Blog 1"
date: 2026-11-16
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# A Model That Runs a Few Seconds a Day: Do You Need a SageMaker Endpoint?

*Trinh Tien Khai · Posted in the AWS Study Group: [LINK TO BE ADDED AFTER POSTING]*

I have been looking into ways to run machine learning inference on AWS and found something interesting: for many problems, the most important question is not "which model" but "how the model is called". This post records what I read, with an example diagram that numbers each flow so it is easy to follow.

![Nightly batch inference with AWS Lambda container images](/images/3-Blog/blog1-lambda-batch-inference.png)

*Figure 1. Example of a nightly batch inference pipeline, drawn by me in draw.io based on AWS documentation.*

**Scenario:** an online store wants to forecast tomorrow's number of orders for each warehouse. It runs once a night after the day's data is closed, the model runs for a few seconds, and the results are written to a database for a dashboard to read. Nobody needs the model to answer on every click.

The first reflex is usually "it's an ML model, so put it on a SageMaker endpoint". But after reading how it is billed, I found this choice does not fit this kind of workload.

## 1. Four ways to run inference on SageMaker

- **Real-time endpoint:** billed per instance-hour for as long as the endpoint is running, even with no requests. Good for low latency, steady traffic, or when you need a GPU.
- **Serverless Inference:** scales to zero when idle, at the cost of cold starts. Memory from 1 GB to 6 GB and no GPU.
- **Asynchronous Inference:** requests are queued; good for large payloads or long processing times.
- **Batch Transform:** runs in batch over a whole dataset, then shuts down.

For a job that runs a few seconds a day, a real-time endpoint is mostly paying for a machine that sits idle. Batch Transform or Serverless Inference would work, but if the model is small and runs on CPU there is a simpler option: AWS Lambda.

## 2. Why a Lambda container image, not a zip package

- A Lambda zip package is limited to 250 MB unzipped (including layers). Data libraries such as pandas, pyarrow and scikit-learn easily hit that ceiling together.
- A container image can be up to 10 GB (uncompressed, including all layers).
- The maximum timeout is still 15 minutes. Moving to a container does not change this limit.

What I like most: the same image runs on a local machine for testing before it is pushed to Amazon ECR, so "it runs on my machine" and "it runs on Lambda" are the same thing.

## 3. Following the 7 flows in the diagram

1. Build the image, pin the base image by digest, tag it with the commit SHA and push it to Amazon ECR.
2. EventBridge Scheduler runs on a nightly cron schedule and invokes the prepare Lambda asynchronously.
3. The prepare Lambda collects and cleans the data and writes it to S3 by date (`data/dt=…/`).
4. When prepare finishes, a Lambda destination invokes the inference Lambda. Both OnSuccess and OnFailure can be wired: if one data source fails, inference still runs for the data that is fine.
5. The inference Lambda reads the data and the model weights in `models/`.
6. The results are written to DynamoDB for the dashboard or API to read.
7. Logs and metrics go to CloudWatch: one summary log line per run and an alarm on the Errors metric are enough to know the next morning how last night's run went.

The red box at the bottom of the diagram is the 24/7 real-time endpoint option, which does not fit this workload.

## 4. What I found while reading the documentation

**A small model does not need to bring the whole framework to Lambda.** For linear models or small networks, you can train elsewhere, export the weights to a file (for example `.npz`), and at prediction time only NumPy is needed for the matrix multiplications. The image is much smaller and starts faster.

**A non-AWS base image needs the runtime interface client installed.** The AWS base images (`public.ecr.aws/lambda/python`) already include it. If you use another image such as `python:3.12-slim`, you must install `awslambdaric`; without it Lambda cannot call the handler.

```dockerfile
# Simplified illustration
FROM python:3.12-slim@sha256:<digest>
WORKDIR /function
COPY requirements.txt .
RUN pip install --no-cache-dir awslambdaric -r requirements.txt
COPY src/ ./src/
ENTRYPOINT ["python", "-m", "awslambdaric"]
CMD ["src.inference.handler"]
```

One more detail I found while testing: `awslambdaric` looks for the handler module relative to the container's working directory. If the working directory is not where `src/` lives, Lambda reports `Runtime.ImportModuleError`. So it is better to declare `WORKDIR` explicitly instead of relying on the base image default.

**Asynchronous invocations are retried by Lambda.** By default Lambda retries twice when the function returns an error. The retry count can be set from 0 to 2, and the maximum event age from 60 seconds to 6 hours. For an inference job, setting retries to 0 is usually more sensible: on error it stops, the alarm fires, and the operator reads the logs and reruns it in a controlled way.

**Destinations only apply to asynchronous invocations.** For direct function invocations, Lambda destinations only apply to the asynchronous type (event source mappings for Kinesis, DynamoDB Streams and Kafka have their own OnFailure destination). The destination can be another Lambda, SQS, SNS, EventBridge, or S3 (S3 only for OnFailure). An easy trap: `aws lambda invoke` is synchronous (`RequestResponse`) by default, so when run by hand the first function finishes but the destination never fires. To rerun the whole chain you must call:

```bash
aws lambda invoke --function-name <prepare> --invocation-type Event out.json
```

## 5. Cost

Lambda has a free tier of 1 million requests and 400,000 GB-seconds per month that does not expire after 12 months like many other services (shared across the account). For example, a 1 GB function that always runs the full 120 seconds once a night uses only about 3,600 GB-seconds a month, less than 1% of the free tier. ECR charges for image storage by size, so set a lifecycle policy to keep only the last few images.

## Which option when

| Workload | Option |
|---|---|
| Infrequent batch runs, small model, CPU only, under 15 minutes | Lambda container image |
| Irregular traffic, cold starts acceptable, no GPU needed | SageMaker Serverless Inference |
| Large payloads or long processing | SageMaker Asynchronous Inference |
| Scoring a large dataset in batch | SageMaker Batch Transform |
| Constant millisecond latency, large model or GPU needed | SageMaker real-time endpoint |

## Conclusion

Choose a service by how the workload actually runs, not by the label "this is an ML problem". For a forecasting job that runs a few seconds each night, a Lambda container image has enough room for the libraries, runs exactly the same locally and costs almost nothing. The rest is understanding the invocation type (synchronous or asynchronous), because many Lambda features such as destinations and retries only apply to one type.

*Note: service prices and limits may change, so check the AWS Pricing pages and the latest documentation before applying this. The Dockerfile is a simplified illustration.*

### References

- [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html)
- [Create a Lambda function using a container image](https://docs.aws.amazon.com/lambda/latest/dg/images-create.html)
- [Lambda asynchronous invocation](https://docs.aws.amazon.com/lambda/latest/dg/invocation-async.html)
- [AWS Lambda pricing](https://aws.amazon.com/lambda/pricing/)
- [SageMaker Serverless Inference](https://docs.aws.amazon.com/sagemaker/latest/dg/serverless-endpoints.html)
- [Inference options in Amazon SageMaker AI](https://docs.aws.amazon.com/sagemaker/latest/dg/deploy-model-options.html)

`#AWS #AWSLambda #AmazonECR #AmazonSageMaker #Serverless #MLOps #AWSStudyGroup #FCAJ`
