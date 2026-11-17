---
title: "Blog 2"
date: 2026-11-16
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Amazon S3 Versioning: Reproducing Data Processing Results When Files Get Overwritten

*Trinh Tien Khai · Posted in the AWS Study Group: [LINK TO BE ADDED AFTER POSTING]*

A business may run a data pipeline every night: download sales data, clean it, then write it to Amazon S3. The data source sometimes corrects figures from a few days earlier, so the pipeline has to reload and overwrite the old files. A few months later, when a model evaluation needs to be checked again, the question "which data did that run use?" becomes hard to answer, because the file name is the same but the content has changed.

Amazon S3 Versioning keeps old versions of an object when it is overwritten or deleted. Combined with recording the VersionId and the SHA-256 hash before every important run, a business can reproduce the exact input data of any processing run.

![S3 data lake and S3 Versioning](/images/3-Blog/blog2-s3-versioning.png)

*Figure 1. Example diagram: pinning the exact data version to reproduce a run, drawn by me in draw.io based on AWS documentation.*

## 1. How the data is organized

Instead of storing everything in one place, the bucket is split into layers by processing stage:

| Layer | Content |
|---|---|
| `raw/` | Original downloaded data, kept as is, never modified. |
| `clean/<dataset>/dt=YYYY-MM-DD/` | Cleaned data, partitioned by date. |
| `features/` | Precomputed features for reuse. |
| `models/<name>/<version>/` | Model weights, configuration and manifest. |
| `eval/` | Evaluation results, stored as Parquet. |

For example, if an evaluation result looks abnormal, you can trace back from `eval/` to `clean/` and then to `raw/` to find which step went wrong. Rows rejected during cleaning should not be deleted either, but moved to a separate file (for example `rejected.parquet`) so the reason can be checked later.

## 2. How S3 Versioning works

A bucket has three states: unversioned, enabled and suspended. Once enabled, it can never return to unversioned; it can only be suspended.

With Versioning enabled, each object has its own version ID:

| Operation | Result |
|---|---|
| Overwrite the same key | The new version becomes current; the old one becomes noncurrent but stays intact. |
| Delete without a VersionId | S3 only adds a delete marker; older versions remain. |
| GetObject without a VersionId | Returns the current version. |
| GetObject with a VersionId | Returns exactly the specified version. |
| Object written before Versioning was enabled | Has the version ID "null" and can still be read with that ID. |

## 3. How the process works

The example architecture has these main steps:

1. **Collect data:** AWS Lambda pulls data from an external API and the operational database.
2. **Store the original data:** the downloaded data is written to `raw/` and kept as is. This is the final reference point if later steps fail.
3. **Clean the data:** cleaned data is written to `clean/<dataset>/dt=…/`. If a source fails, the system keeps the old data and flags it as "stale" instead of overwriting it with incomplete data.
4. **Daily processing:** the daily job reads the current version with GetObject without a VersionId, because this flow always needs the latest data.
5. **Pin the data version:** before an important run, such as a model evaluation, each input file is "pinned" by recording its key, VersionId and SHA-256 in a run plan.
6. **Read exactly the pinned version:** the runner reads the file with GetObject and the VersionId, then checks the SHA-256 before computing.
7. **Store the results:** results are written as Parquet to `eval/`.

Steps 4 and 6 read the same key but answer two different questions: step 4 asks "what is the data now", while step 6 asks "what was the data when it was pinned".

## 4. Role of each component

| Component | Role |
|---|---|
| AWS Lambda | Collect and clean data |
| Amazon S3 | Store data in the `raw/`, `clean/`, `features/`, `models/`, `eval/` layers |
| S3 Versioning | Keep old versions when objects are overwritten or deleted |
| Run plan | Record the key, VersionId and SHA-256 of the input data |
| Evaluation runner | Read exactly the pinned version and check integrity |
| IAM | Grant permission to read the current version or a specific version |
| S3 Lifecycle | Clean up old versions to control cost |

## 5. System design issues worth noting

### Pin with both the VersionId and SHA-256

The VersionId tells you which object to read on S3, while SHA-256 proves the content is exactly what was pinned, even after the file has been downloaded and copied elsewhere. The VersionId can be taken from the PutObject or HeadObject response once the bucket has Versioning enabled.

Sample code for reading a pinned version:

```python
import hashlib
import boto3

s3 = boto3.client("s3")

def read_pinned(bucket: str, key: str, version_id: str, expected_sha256: str) -> bytes:
    # Read exactly the pinned version, not the latest one
    obj = s3.get_object(Bucket=bucket, Key=key, VersionId=version_id)
    body = obj["Body"].read()
    if hashlib.sha256(body).hexdigest() != expected_sha256:
        raise RuntimeError(f"SHA-256 mismatch: {key}")
    return body
```

### S3's built-in checksums do not replace a self-computed SHA-256

S3 also stores checksums for objects: if an upload has no checksum, S3 adds a CRC64NVME checksum for the whole object. You can choose SHA-256 when uploading, but for multipart uploads SHA-256 is only available as a composite checksum (built from the checksums of each part), so it cannot be compared directly with a SHA-256 of the whole file. In addition, S3 returns checksums as the base64 of the digest, unlike the hex string returned by `hashlib.hexdigest()`. So computing SHA-256 on the client side is still the most reliable way to pin.

### IAM permissions for reading by version

The `s3:GetObject` permission is only enough to read the current version. Reading a specific version by VersionId requires `s3:GetObjectVersion`, a separate permission not included in `s3:GetObject`. Listing versions also requires `s3:ListBucketVersions`. Without them, the runner gets AccessDenied even though the daily flow keeps working normally.

### Storage cost

Every version is billed for storage like a normal object, so a versioned bucket grows over time. A lifecycle rule with `NoncurrentVersionExpiration` can delete old versions after a number of days, combined with `NewerNoncurrentVersions` to always keep the N most recent versions. However, make sure the rule does not delete pinned versions while they are still needed for reproduction.

## 6. System design lessons

S3 Versioning shows that "keeping old data" and "being able to reproduce results" are two different things. Versioning only keeps versions; to reproduce, the system must actively record which version was used.

A reproducible pipeline follows this flow:

```text
Data Source → raw/ → clean/ → Pin (VersionId + SHA-256) → Evaluation → eval/
```

Before every important run, check:

- Was Versioning enabled before the overwrites or deletions you need to keep?
- Were the key, VersionId and SHA-256 recorded in the run plan before running?
- Does the runner's role have `s3:GetObjectVersion`?
- Could a lifecycle rule delete a pinned version?
- Were the code and configuration (commit SHA, config file hash) pinned together with the data? Correct data with different code still gives different results.

A note for current deployments: since 5 January 2023, Amazon S3 automatically encrypts all new objects with SSE-S3 and this cannot be turned off, so when designing a bucket you only need to block public access, accept HTTPS only, and decide whether to switch to SSE-KMS. Also, objects that existed before Versioning was enabled can still be pinned with VersionId "null", but versions overwritten before it was enabled are already lost.

### AWS References

- [How S3 Versioning works](https://docs.aws.amazon.com/AmazonS3/latest/userguide/versioning-workflows.html)
- [Retrieving object versions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/RetrievingObjectVersions.html)
- [Deleting object versions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/DeletingObjectVersions.html)
- [GetObject API](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html)
- [Checking object integrity](https://docs.aws.amazon.com/AmazonS3/latest/userguide/checking-object-integrity.html)
- [Managing the lifecycle of objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html)
- [Default encryption FAQ](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-encryption-faq.html)
