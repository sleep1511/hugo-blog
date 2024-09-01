---
title: "A Quick Overview AWS S3 Bucket"
date: 2022-11-11T08:17:26+07:00
draft: false
tags: "AWS"
categories: "technical"
tranding: false
readTime: "15 min"
thumbnail: /images/blog/s3-overview/thum.png
featureImage: /images/blog/s3-overview/feature.jpeg
---

Chắc bạn đã làm việc và quá quen thuộc với AWS S3, bài viết này mong muốn giúp bạn overview lại kiến thức và có thể tối ưu chi phí hơn khi sử dụng với S3 AWS

#### What is AWS S3? 

AWS S3 là gì 🤷? AWS S3 (Amazon Simple Storage Service) một dịch vụ lưu trữ file như image, videos, document,... trên nền tảng AWS Cloud có thể truy cập ở mọi nơi trên thế giới. S3 có tính bảo mật cao, hầu như không lo ngại việc dữ liệu của bạn bị mất (durable), dễ dàng scale, linh hoạt về chi phí sử dụng theo nhu cầu.

S3 là universal trên toàn thế giới nhưng khi tạo một bucket (bể chứa) S3 bạn phải chọn một region cụ thể.

Tính nhất quán consistency của S3. Như bạn đã biết, khi upload lên thành công S3 sẽ trả về status 200, khi upload mới một object (tên object chưa tồn tại trong bucket) thì bạn có thể truy xuất ngay được object đó. Nhưng khi upload lên cùng một file name đó lên bucket thì S3 sẽ ghi đè lên object hiện tại và bạn cần phải chờ một khoảng thời gian thì mới lấy được object mới nhất `READ AFTER WRITE`, nếu object đó được caching đến nhiều CloudFront thì phải mất ít nhất (24h) mới có data mới. Và để CloudFront cập nhật ngay thì phải tạo một validation (có tính phí).

S3 guarantess! (Remmber 11x9s): tức là khả năng object bị mất hầu như bằng 0 (Đề thi AWS SSA-C02)

#### The basic of S3

✅ Dung lượng mỗi file lên đến 5TB

✅ Không giới hạn lưu trữ

✅ Hổ trợ torrent (nôm na là công nghệ lưu trữ file lớn ở nhiều nơi, trong quá trình download nếu một source bị đứt quãng thì file vẫn đc tải về qua các source khác)

✅ Meta data: dùng để lưu trữ một số thông tin thêm cho object, hữu ích trong bài toán lưu trữ shorten url hoặc owrn của file, hay đơn giản chỉ là content-type của media file

Quyền truy cập mặc định khi tạo một bucket là private. IT Team phải cập nhật Bucket Policy, Access Ctrol list (ACls) để public những object trong bucket đó ra ngoài

Cấu trúc URL của một S3 object sẽ là (Đề thi AWS SSA-C02):

`http://[bucket_name].[region_name].amazon.com`

*Lưu ý: Với các bucket ở region Virginia thì không có region_name.*

#### S3 Storage Classes

****[Performance across the S3 Storage Classes](https://aws.amazon.com/s3/storage-classes/)**** (2022)

|  | S3 Standard | S3 Intelligent-Tiering* (recommend) | S3 Standard-IA (Infrequently Accessed) | S3 One Zone-IA† | S3 GlacierInstant Retrieval | S3 Glacier Flexible Retrieval | S3 GlacierDeep Archive |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Designed for durability | 99.999999999%(11 9’s) | 99.999999999%(11 9’s) | 99.999999999%(11 9’s) | 99.999999999%(11 9’s) | 99.999999999%(11 9’s) | 99.999999999%(11 9’s) | 99.999999999%(11 9’s) |
| Designed for availability | 99.99% | 99.9% | 99.9% | 99.5% | 99.9% | 99.99% | 99.99% |
| Availability SLA | 99.9% | 99% | 99% | 99% | 99% | 99.9% | 99.9% |
| Availability Zones | ≥3 | ≥3 | ≥3 | 1 | ≥3 | ≥3 | ≥3 |
| Minimum capacity charge per object | N/A | N/A | 128 KB | 128 KB | 128 KB | 40 KB | 40 KB |
| Minimum storage duration charge | N/A | N/A | 30 days | 30 days | 90 days | 90 days | 180 days |
| Retrieval charge | N/A | N/A | per GB retrieved | per GB retrieved | per GB retrieved | per GB retrieved | per GB retrieved |
| First byte latency | milliseconds | milliseconds | milliseconds | milliseconds | milliseconds | minutes or hours | hours |
| Storage type | Object | Object | Object | Object | Object | Object | Object |
| Lifecycle transitions | Yes | Yes | Yes | Yes | Yes | Yes | Yes |


Chú thích:

* *Minimum capacity charge per object: phí tối thiểu theo dung lượng object*

* *Minimum storage duration charge: thời gian lưu trữ tối thiểu*

* *First byte latency: thời gian lấy object*

#### S3 Charge

Cách tính phí của S3, anh em thường thường không để ý chổ này *(vì giá S3 rẻ như cho mà 🤪)* nhưng ngoài cách tính phí lưu trữ ra, S3 còn tính thêm nhiều loại phí khác mà đối với các dự án lớn về lưu trữ hay mua bán ảnh thì cần cân đo đong đếm lại 💵

#### S3 versioning bucket

Có những tai nạn như xoá nhầm file,... thì có thể giúp bạn khôi phục được trong tình huống đó. Bạn không thể enable/disable tuỳ ý được, một khi bạn enable thì muốn disble tính năng này thì bạn không còn cách nào khác là detect nó (bay hết các version đi luôn T_T)

#### S3 Object lock
Bạn có thể sử dụng S3 Object lock để lưu trữ object với chế độ `write one, read many (WORM)`. Tức là chỉ upload lên một lần và sử dụng, nhằm ngăn chặn ai đó chỉnh sửa hoặc xoá file đi.

S3 Object lock có 2 mode:

1. Governance mode (quản trị): user ko thể override hoặc modify, có thể cấp quyền cho user để xoá và update

2. Compliance Mode (tuân thủ nghiêm ngặc): Là mode nghiêm ngặc hơn govermance không thể xoá ngay cả root user, không thể thay đổi mode này được

Ủa vậy thì khi nào mới được xoá, lỡ tay setting thì bó tay à 🤔? → Khi đã hết hạn [Retention Previod](https://aws.amazon.com/blogs/storage/how-to-manage-retention-periods-in-bulk-using-amazon-s3-batch-operations/) thì bạn có thể xoá đi nhé

#### S3 replicate bucket

Một lưu ý nhỏ là để sử dụng option này thì bắt buộc bucket source và bucket destination đều phải bật mode versionning nhé. Nếu không thì khi setting thì nó sẽ có alert để bật versioning của bucket lên.

#### S3 Life cycle

♻️

#### S3 Transfer Acceleration
Mục đích tăng tốc độ upload. Sử dụng hạ tầng Edge Location,

[Step by step at h](https://www.youtube.com/watch?v=mt0YAd6kB7k)

#### Encryption S3
Encryption S3 là dạng mã hoá ổ cứng vật lý trên máy chủ S3. Đối với những dữ liệu quan trọng thuộc tổ chức lớn hay chính phủ chẳng hạn, thì dù AWS hay bất kỳ dịch vụ cloud nào khác có đảm bảo đến đâu thì họ cũng không thể hoàn toàn tin tưởng S3. S3 cũng là một compute vật lý vậy nếu có ai đó lấy được được ổ cứng đó thì sao? Không gì là không thể. S3 Encryption sinh ra để giải quyết bài toán đó của khách hàng.

Encryption at Rest: mã hoá đầu cuối.

- SSE-S3: bạn tin tưởng và để S3 mã hoá và tự quản lý
- SSE-KMS: dịch vụ mã hoá riêng của AWS
- SSE-C with customer provider key: user cung cấp key để mã hoá. Không có trên AWS console mà sử dụng AWS CLI, SDK (Software Development Kit) để dùng.

Client Side Encryption: Mình tự mã hoá trước khi upload và tự giải mã sau khi lấy về

2 level mã hoá là bucket và object

#### Multipart upload

Làm sao để upload một file với dung lượng lớn cả 100MB cả Gb lên bucket? S3 Multipart upload sẽ giúp chúng ta giải quyết được điều đó.
Nôm na là AWS sẽ chia các file thành các phần (part) nhỏ để upload lên và nối các part đó thành một file hoàn chỉnh khi tất cả đã hoàn thành upload.
Ngoài ra S3 còn hổ trợ cơ chế parallelize upload, giúp chúng ta upload bằng nhiều thread cùng một lúc, giảm thời gian chờ, bạn có thể thiết kế một background job để cải thiện UI/UX, tốc độ upload để tạo thiện cảm trong mắt người dùng.😜

Bạn có thể tham khảo bài [lab](https://medium.com/m/global-identity?redirectUrl=https%3A%2F%2Faws.plainenglish.io%2Foptimize-your-aws-s3-performance-27b057f231a3) này

Một số điểm nhò cần lưu ý:
* Trước tiên bạn phải cài AWS CLI để sử dụng tính năng này
* Mỗi part ít nhất 5Mb, tuy nhiên part cuối cùng có thể nhỏ hơn

Ngược lại với Multipart upload là **S3 byte-range fetches** giúp việc tải từng part tăng perfomance (cũng hỗ trợ parallelize) khi download

#### S3 Perfomance

##### S3 Prefix

#### Summary

AWS S3 vẫn còn nhiều tính năng nữa và với chừng ấy dịch vụ trên cũng đã đủ đáp ứng được được nhu cầu của tất cả khách hàng, dù khó tính nhất. 