---
title: "Blog 3"
date: 2026-11-16
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Thiết kế bảng DynamoDB: bắt đầu từ câu hỏi, không phải từ dữ liệu

*Trịnh Tiến Khải · Bài đăng trên nhóm AWS Study Group: [CẬP NHẬT LINK SAU KHI ĐĂNG]*

Quen làm việc với SQL, lúc mới học Amazon DynamoDB mình thiết kế bảng theo thói quen cũ: chuẩn hóa dữ liệu, mỗi dòng một con số, cần gì thì lọc. Đọc kỹ tài liệu thì mình phát hiện DynamoDB đòi cách nghĩ ngược lại: phải biết trước ứng dụng sẽ hỏi gì rồi mới chọn khóa. Bài này ghi lại cách tiếp cận đó qua một ví dụ, kèm sơ đồ có đánh số từng luồng, và một tính năng mình thấy rất đáng biết: ghi có điều kiện.

![DynamoDB: chọn khóa theo câu hỏi và ghi có điều kiện](/images/3-Blog/blog3-dynamodb-key-design.png)

*Hình 1. Sơ đồ ví dụ một ứng dụng dự báo thời tiết đọc và ghi bảng DynamoDB, mình vẽ bằng draw.io dựa trên tài liệu AWS.*

**Tình huống:** một ứng dụng thời tiết mỗi đêm chạy job tính dự báo 7 ngày tới cho từng thành phố. Web và API cần hiển thị dự báo mới nhất. Nếu lưu theo kiểu SQL (mỗi dòng là một thành phố, một ngày, một chỉ số), trang chủ muốn hiện dự báo của một thành phố sẽ phải đọc hàng chục item hoặc quét bảng. Với DynamoDB, đó là cách thiết kế vừa chậm vừa tốn tiền.

## 1. Liệt kê câu hỏi trước

Viết ra các câu hỏi ứng dụng sẽ hỏi:

- "Dự báo mới nhất của Hà Nội là gì?" (trang chủ)
- "Dự báo được tạo ngày 2026-08-01 cho Đà Nẵng là gì?" (xem lại lịch sử)
- Có câu nào cần quét toàn bảng không? Ở đây là không.

## 2. Chọn khóa

- **Partition key:** `city`
- **Sort key:** `forecast_date`, dạng chuỗi `YYYY-MM-DD`. Định dạng này sắp xếp theo chữ cũng chính là sắp xếp theo thời gian.

Câu hỏi đầu tiên trở thành một Query đọc ngược, lấy đúng 1 item:

```python
# Minh họa rút gọn
import boto3
from boto3.dynamodb.conditions import Key

table = boto3.resource("dynamodb").Table("Forecasts")

def latest(city: str) -> dict | None:
    resp = table.query(
        KeyConditionExpression=Key("city").eq(city),
        ScanIndexForward=False,  # forecast_date giảm dần
        Limit=1,
    )
    items = resp.get("Items", [])
    return items[0] if items else None
```

Câu hỏi thứ hai là GetItem với đủ hai khóa. Không cần GSI, không cần Scan.

## 3. Một item chứa gì

Thay vì mỗi dòng một con số, gom trọn một lần dự báo của một thành phố vào một item: 7 ngày tới, mỗi ngày gồm nhiệt độ, lượng mưa, độ ẩm, kèm phiên bản mô hình và thời điểm tạo. Mỗi lần đọc chỉ tốn một request, và ứng dụng không phải ghép nhiều dòng. Chỉ cần để ý giới hạn kích thước một item là 400 KB.

Một chi tiết nhỏ: với boto3 resource, số phải truyền vào dạng `Decimal`, truyền `float` sẽ báo lỗi. Khi làm tròn số trước khi ghi, nhớ rằng số đọc ra có thể lệch rất nhỏ so với số tính trong bộ nhớ, nên khi so sánh hãy dùng dung sai, đừng so bằng tuyệt đối.

## 4. Đi theo 7 luồng trên sơ đồ

1. Người dùng mở web trên AWS Amplify Hosting và đăng nhập bằng Amazon Cognito để nhận ID token.
2. Web gọi API Gateway (REST) với header `Authorization: <ID token>`. Cognito authorizer kiểm token trước khi cho request đi tiếp.
3. Lambda api đọc bảng bằng Query `Limit=1` (dự báo mới nhất) hoặc GetItem (theo ngày).
4. EventBridge Scheduler chạy job dự báo mỗi đêm.
5. Lambda batch ghi kết quả bằng PutItem có điều kiện.
6. Nếu job bị chạy lại (chạy tay, hoặc Lambda tự thử lại), lần ghi thứ hai bị từ chối với `ConditionalCheckFailedException`. Code bắt lỗi này và coi như "đã có", không coi là sự cố.
7. Mô hình thử nghiệm chạy song song trên cùng dữ liệu nhưng ghi sang S3, không ghi vào bảng phục vụ. Người dùng chỉ thấy kết quả của mô hình chính, còn mô hình mới vẫn được chấm điểm trên dữ liệu thật.

## 5. Ghi có điều kiện: không ghi đè bản ghi đã có

DynamoDB không có lệnh "insert". PutItem mặc định ghi đè nếu đã có item cùng khóa. Muốn mỗi cặp (`city`, `forecast_date`) chỉ được ghi một lần thì thêm `ConditionExpression`:

```python
# Minh họa rút gọn
from botocore.exceptions import ClientError

def put_once(item: dict) -> bool:
    try:
        table.put_item(
            Item=item,
            ConditionExpression="attribute_not_exists(city) AND attribute_not_exists(forecast_date)",
        )
        return True
    except ClientError as e:
        if e.response["Error"]["Code"] == "ConditionalCheckFailedException":
            return False  # đã có bản ghi cho ngày này
        raise
```

Ba điều mình phát hiện khi đọc tài liệu:

- Điều kiện được kiểm ngay trên item đó và thực hiện nguyên tử cùng lần ghi, nên không cần "đọc trước rồi mới ghi". Cách đọc trước rồi ghi vẫn có thể bị một tiến trình khác chen vào giữa.
- Lần ghi bị từ chối vẫn tiêu tốn write capacity, số lượng tùy kích thước item.
- Có tham số `ReturnValuesOnConditionCheckFailure` để DynamoDB trả về item hiện có ngay khi điều kiện sai, đỡ phải gọi thêm một lần đọc để xem chuyện gì đã xảy ra.

## Checklist khi thiết kế bảng DynamoDB

- Đã liệt kê hết câu hỏi ứng dụng sẽ hỏi chưa? Mỗi câu là Query hay GetItem?
- Sort key có sắp xếp đúng thứ tự mình cần không (ví dụ ngày dạng `YYYY-MM-DD`)?
- Có câu nào bắt buộc phải Scan không? Nếu có, xem lại khóa hoặc thêm GSI.
- Item có thể vượt 400 KB không?
- Ghi đè có phải là hành vi mình muốn không? Nếu không, dùng `ConditionExpression`.
- So sánh số đọc ra từ DynamoDB đã dùng dung sai chưa?

## Kết luận

Với DynamoDB, chọn khóa đúng quan trọng hơn mọi thứ khác. Bắt đầu từ câu hỏi thì mọi lần đọc đều là Query hoặc GetItem, không cần GSI hay Scan. Còn ghi có điều kiện là cách rẻ và an toàn nhất để có ngữ nghĩa "chỉ ghi một lần", rất hợp với các job batch có thể bị chạy lại.

*Lưu ý: giá và giới hạn dịch vụ có thể thay đổi, nên kiểm lại tài liệu AWS mới nhất. Đoạn code là minh họa rút gọn.*

### Tài liệu tham khảo

- [Condition expressions](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.ConditionExpressions.html)
- [DynamoDB read and write operations (capacity khi điều kiện sai)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/read-write-operations.html)
- [PutItem API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_PutItem.html)
- [Query](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html)
- [Best practices for designing partition keys](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-partition-key-design.html)
- [Handle conditional write errors in high concurrency scenarios](https://aws.amazon.com/blogs/database/handle-conditional-write-errors-in-high-concurrency-scenarios-with-amazon-dynamodb)

`#AWS #AmazonDynamoDB #APIGateway #AmazonCognito #Serverless #NoSQL #AWSStudyGroup #FCAJ`
