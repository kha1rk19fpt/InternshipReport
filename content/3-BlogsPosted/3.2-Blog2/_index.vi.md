---
title: "Blog 2"
date: 2026-11-16
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Amazon S3 Versioning: tái lập kết quả xử lý dữ liệu khi file bị ghi đè

*Trịnh Tiến Khải · Bài đăng trên nhóm AWS Study Group: [CẬP NHẬT LINK SAU KHI ĐĂNG]*

Một doanh nghiệp có thể chạy pipeline dữ liệu mỗi đêm: tải dữ liệu bán hàng, làm sạch rồi ghi vào Amazon S3. Nguồn dữ liệu đôi khi sửa số liệu của vài ngày trước, nên pipeline phải tải lại và ghi đè lên file cũ. Vài tháng sau, khi cần kiểm tra lại một lần đánh giá mô hình, câu hỏi "lần đó đã dùng dữ liệu nào?" trở nên khó trả lời, vì tên file vẫn vậy nhưng nội dung đã khác.

Amazon S3 Versioning giữ lại các phiên bản cũ của object khi bị ghi đè hoặc xóa. Kết hợp với việc ghi lại VersionId và mã băm SHA-256 trước mỗi lần chạy quan trọng, doanh nghiệp có thể tái lập chính xác dữ liệu đầu vào của bất kỳ lần xử lý nào.

![S3 data lake và S3 Versioning](/images/3-Blog/blog2-s3-versioning.png)

*Hình 1. Sơ đồ ví dụ: ghim đúng phiên bản dữ liệu để tái lập một lần chạy, mình vẽ bằng draw.io dựa trên tài liệu AWS.*

## 1. Tổng quan cách tổ chức dữ liệu

Thay vì lưu mọi thứ chung một chỗ, bucket được chia thành các tầng theo mức độ xử lý:

| Tầng | Nội dung |
|---|---|
| `raw/` | Dữ liệu gốc tải về, giữ nguyên, không sửa. |
| `clean/<dataset>/dt=YYYY-MM-DD/` | Dữ liệu đã làm sạch, phân vùng theo ngày. |
| `features/` | Đặc trưng tính sẵn để dùng lại. |
| `models/<name>/<version>/` | Trọng số, cấu hình và manifest của mô hình. |
| `eval/` | Kết quả đánh giá, lưu dạng Parquet. |

Ví dụ, nếu kết quả đánh giá bất thường, có thể lần ngược từ `eval/` về `clean/` rồi về `raw/` để biết lỗi nằm ở bước nào. Dòng dữ liệu bị loại khi làm sạch cũng không nên xóa mà chuyển sang một file riêng (ví dụ `rejected.parquet`) để kiểm tra lại lý do.

## 2. Cách S3 Versioning hoạt động

Một bucket có ba trạng thái: chưa bật (unversioned), đã bật (enabled) và tạm dừng (suspended). Khi đã bật thì không thể quay về trạng thái chưa bật, chỉ có thể tạm dừng.

Khi Versioning được bật, mỗi object có một version ID riêng:

| Thao tác | Kết quả |
|---|---|
| Ghi đè cùng một key | Bản mới thành current, bản cũ thành noncurrent nhưng vẫn còn nguyên. |
| Xóa không kèm VersionId | S3 chỉ đặt một delete marker, các phiên bản cũ vẫn còn. |
| GetObject không kèm VersionId | Trả về bản current. |
| GetObject kèm VersionId | Trả về đúng phiên bản được chỉ định. |
| Object ghi trước khi bật Versioning | Có version ID là "null" và vẫn đọc lại được theo ID này. |

## 3. Quy trình hoạt động

Kiến trúc ví dụ được chia thành các bước chính:

1. **Thu thập dữ liệu:** AWS Lambda lấy dữ liệu từ API bên ngoài và database nghiệp vụ.
2. **Lưu dữ liệu gốc:** dữ liệu tải về được ghi vào `raw/` và giữ nguyên. Đây là điểm tham chiếu cuối cùng nếu các bước sau có lỗi.
3. **Làm sạch dữ liệu:** dữ liệu sau khi làm sạch được ghi vào `clean/<dataset>/dt=…/`. Nếu một nguồn bị lỗi, hệ thống giữ dữ liệu cũ và gắn cờ "stale" thay vì ghi đè bằng dữ liệu thiếu.
4. **Xử lý hằng ngày:** job hằng ngày đọc bản hiện tại bằng GetObject không kèm VersionId, vì luồng này luôn cần dữ liệu mới nhất.
5. **Ghim phiên bản dữ liệu:** trước một lần chạy quan trọng, chẳng hạn đánh giá mô hình, từng file đầu vào được "ghim" bằng cách ghi key, VersionId và SHA-256 vào một kế hoạch chạy (run plan).
6. **Đọc đúng phiên bản đã ghim:** runner đọc file bằng GetObject kèm VersionId, sau đó kiểm tra SHA-256 trước khi tính toán.
7. **Lưu kết quả:** kết quả được ghi dạng Parquet vào `eval/`.

Bước 4 và Bước 6 cùng đọc một key nhưng trả lời hai câu hỏi khác nhau: Bước 4 hỏi "bây giờ dữ liệu là gì", còn Bước 6 hỏi "lúc ghim, dữ liệu là gì".

## 4. Vai trò của các thành phần

| Thành phần | Vai trò |
|---|---|
| AWS Lambda | Thu thập và làm sạch dữ liệu |
| Amazon S3 | Lưu dữ liệu theo tầng `raw/`, `clean/`, `features/`, `models/`, `eval/` |
| S3 Versioning | Giữ lại phiên bản cũ khi object bị ghi đè hoặc xóa |
| Run plan | Ghi lại key, VersionId và SHA-256 của dữ liệu đầu vào |
| Runner đánh giá | Đọc đúng phiên bản đã ghim và kiểm tra tính toàn vẹn |
| IAM | Cấp quyền đọc bản hiện tại hoặc đọc theo phiên bản |
| S3 Lifecycle | Dọn dẹp phiên bản cũ để kiểm soát chi phí |

## 5. Một số vấn đề System Design đáng chú ý

### Ghim bằng cả VersionId và SHA-256

VersionId cho biết cần đọc object nào trên S3, còn SHA-256 chứng minh nội dung đúng như lúc ghim, kể cả khi file đã được tải về rồi chép sang nơi khác. VersionId có thể lấy từ phản hồi của PutObject hoặc HeadObject khi bucket đã bật Versioning.

Đoạn code minh họa cách đọc một phiên bản đã ghim:

```python
import hashlib
import boto3

s3 = boto3.client("s3")

def read_pinned(bucket: str, key: str, version_id: str, expected_sha256: str) -> bytes:
    # Lấy đúng phiên bản đã ghim, không phải bản mới nhất
    obj = s3.get_object(Bucket=bucket, Key=key, VersionId=version_id)
    body = obj["Body"].read()
    if hashlib.sha256(body).hexdigest() != expected_sha256:
        raise RuntimeError(f"SHA-256 không khớp: {key}")
    return body
```

### Checksum có sẵn của S3 chưa thay thế được SHA-256 tự tính

S3 cũng tự lưu checksum cho object: nếu upload không kèm checksum, S3 tự gắn checksum CRC64NVME cho cả object. Có thể chọn SHA-256 khi upload, nhưng với multipart upload thì SHA-256 chỉ có dạng composite (ghép từ checksum của từng phần), nên không so trực tiếp được với SHA-256 tính trên cả file. Ngoài ra, S3 trả checksum ở dạng base64 của digest, khác chuỗi hex mà `hashlib.hexdigest()` trả về. Vì vậy, tự tính SHA-256 phía client vẫn là cách chắc chắn nhất để ghim.

### Quyền IAM cho việc đọc theo phiên bản

Quyền `s3:GetObject` chỉ đủ để đọc bản current. Muốn đọc một phiên bản cụ thể bằng VersionId thì cần quyền `s3:GetObjectVersion`, đây là một quyền riêng, không nằm trong `s3:GetObject`. Muốn liệt kê các phiên bản thì cần thêm `s3:ListBucketVersions`. Nếu thiếu, runner sẽ gặp lỗi AccessDenied dù luồng xử lý hằng ngày vẫn chạy bình thường.

### Chi phí lưu trữ

Mỗi phiên bản đều được tính phí lưu trữ như một object bình thường, nên bucket có Versioning sẽ lớn dần theo thời gian. Có thể đặt lifecycle rule `NoncurrentVersionExpiration` để xóa phiên bản cũ sau một số ngày, kết hợp `NewerNoncurrentVersions` để luôn giữ lại N phiên bản gần nhất. Tuy nhiên, cần bảo đảm rule không xóa mất các phiên bản đã ghim khi vẫn còn cần tái lập.

## 6. Bài học về System Design

S3 Versioning cho thấy "lưu được dữ liệu cũ" và "tái lập được kết quả" là hai việc khác nhau. Versioning chỉ giữ lại phiên bản; để tái lập, hệ thống cần chủ động ghi lại phiên bản nào đã được dùng.

Một pipeline có thể tái lập sẽ đi theo luồng:

```text
Data Source → raw/ → clean/ → Pin (VersionId + SHA-256) → Evaluation → eval/
```

Trước mỗi lần chạy quan trọng, nên kiểm tra:

- Versioning đã được bật trước những lần ghi đè hoặc xóa cần giữ lại chưa
- Key, VersionId và SHA-256 đã được ghi vào run plan trước khi chạy chưa
- Role của runner có quyền `s3:GetObjectVersion` chưa
- Lifecycle rule có thể xóa phiên bản đã ghim hay không
- Code và cấu hình (commit SHA, hash file cấu hình) đã được ghim cùng dữ liệu chưa, vì dữ liệu đúng mà code khác thì kết quả vẫn khác

Một lưu ý khi triển khai hiện nay: từ ngày 5/1/2023, Amazon S3 tự động mã hóa mọi object mới bằng SSE-S3 và không thể tắt, nên khi thiết kế bucket chỉ cần chặn truy cập công khai, chỉ nhận HTTPS và quyết định có chuyển sang SSE-KMS hay không. Ngoài ra, object có từ trước khi bật Versioning vẫn ghim được với VersionId "null", nhưng những lần ghi đè xảy ra trước khi bật thì phiên bản cũ đã mất.

### AWS References

- [How S3 Versioning works](https://docs.aws.amazon.com/AmazonS3/latest/userguide/versioning-workflows.html)
- [Retrieving object versions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/RetrievingObjectVersions.html)
- [Deleting object versions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/DeletingObjectVersions.html)
- [GetObject API](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html)
- [Checking object integrity](https://docs.aws.amazon.com/AmazonS3/latest/userguide/checking-object-integrity.html)
- [Managing the lifecycle of objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html)
- [Default encryption FAQ](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-encryption-faq.html)
