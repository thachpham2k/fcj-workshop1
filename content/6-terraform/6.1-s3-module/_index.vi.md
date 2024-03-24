---
title : "Tạo Module S3 Website "
menuTitle : "Module staticweb"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 6.1. </b> "
description: "Mô-đun Terraform để tạo S3 với khả năng lưu trữ trang web tĩnh"
---

## Tổng quan

Ở bước này, chúng ta sẽ tạo một bucket S3 và cấu hình nó để hoạt động như một trang web tĩnh.

Bước này tương đương với [Bước 3: CÀI ĐẶT S3 STATIC WEB](https://thachpham2k.github.io/fcj-workshop1/vi/3-s3_staticweb/) trong hướng dẫn.

## Chuẩn bị

1. Resource `random_integer`

    Như đã đề cập trong phần tạo bucket S3, *tên của bucket phải là duy nhất để tránh xung đột tên khi tạo bucket.* Vì vậy, chúng ta thêm một dãy số ngẫu nhiên vào tên bucket. Terraform cung cấp resource `random_integer` để tạo dãy số ngẫu nhiên.

    Resource này yêu cầu 2 argument bắt buộc là `min` và `max` (giới hạn dưới và giới hạn trên của số ngẫu nhiên). Trong trường hợp này, chúng ta cần một số có ba chữ số, vì vậy chúng ta sẽ khai báo như sau:

    ```hcl
    resource "random_integer" "s3-suffix" {
        min = 100
        max = 999
    }
    ```

    {{% notice note %}}
Để tìm hiểu về resource này, hãy truy cập [random_integer (Resource)](https://registry.terraform.io/providers/hashicorp/random/latest/docs/resources/integer).   
Terraform cung cấp nhiều resource để tạo nên các dãy số, ký tự, id,... ngẫu nhiên khác thông qua [Random Provider](https://registry.terraform.io/providers/hashicorp/random/latest/docs).
    {{% /notice %}}

2. Tệp policy

    Khai báo tệp policy (tệp này sẽ được sử dụng ở mục *4. Bucket policy* ở phần **Thực hiện**), phần này sử dụng 1 giá trị đầu vào là `bucket_name` - nó sẽ nhận được giá trị thông qua function templatefile và policy này sẽ được lưu trong tệp có định dạng là `*.tpl`

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Sid": "Statement1",
                "Principal": "*",
                "Effect": "Allow",
                "Action": [
                    "s3:GetObject"
                ],
                "Resource": [
                    "arn:aws:s3:::${bucket_name}/*"
                ]
            }
        ]
    }
    ```

## Thực hiện

Thay vì chỉ có một bước và cung cấp nhiều thông tin như trên giao diện AWS Console, chúng ta phải sử dụng nhiều resource khác nhau để tạo một bucket S3 giống như vậy bằng Terraform. (trước kia có thể  nhưng terraform bản mới buộc phải tách ra).

1. Tạo S3 Bucket

    Tương tự bước **General configuration** ở mục *3.* phần [Thực Hành của CÀI ĐẶT S3 STATIC WEB](https://thachpham2k.github.io/fcj-workshop1/vi/3-s3_staticweb/#th%E1%BB%B1c-h%C3%A0nh)

    ![General configuration](/images/3-s3_staticweb/3.1-create.png)

    ```hcl
    resource "aws_s3_bucket" "s3_bucket" {
        bucket = "${var.project-name}-${random_integer.s3-suffix.result}"
        force_destroy = true
        tags   = var.project-tags
    }
    ```
    
    Như đã đề cập trước đó, tên của S3 Bucket sẽ có định dạng *tên project*-*1 dãy số ngẫu nhiên*. Để nhận một dãy số ngẫu nhiên từ resource `random_integer` đã tạo trước đó, chúng ta sử dụng Attribute `result`.

    Có một argument lạ trong phần này là `force_destroy`. Như **Xóa S3** trong [DỌN DẸP TÀI NGUYÊN](https://thachpham2k.github.io/fcj-workshop1/vi/5-cleanup/#x%C3%B3a-s3) thì để xóa bucket S3, trước tiên phải làm trống (empty) nó. Tương tự, bucket S3 được tạo bằng Terraform sẽ không bị xóa khi chạy lệnh `terraform destroy` vì nó vẫn chứa dữ liệu, và việc sử dụng argument `force_destroy` giúp xóa bucket S3 mà không gặp lỗi nếu nó vẫn chứa dữ liệu. Tuy nhiên, khi sử dụng argument này, hãy cân nhắc vì tính năng không cho phép xóa bucket S3 mặc định giúp đảm bảo bạn không **dại dột** xóa dữ liệu của mình.

2. Object Ownership

    Tương tự như **Object Ownership** ở mục *3.* phần [Thực Hành CÀI ĐẶT S3 STATIC WEB](https://thachpham2k.github.io/fcj-workshop1/vi/3-s3_staticweb/#th%E1%BB%B1c-h%C3%A0nh)

    ![Object ownership](/images/3-s3_staticweb/3.2-create.png)

    ```hcl
    resource "aws_s3_bucket_ownership_controls" "bucket_owner" {
        bucket = aws_s3_bucket.s3_bucket.id
        rule {
            object_ownership = "BucketOwnerPreferred"
        }
    }
    ```

    Không có gì mới ở phần này ngoài việc khai báo giá trị cho argument `bucket` bằng attribute `id` của S3 bucket vừa được tạo trước đó.

3. Block public access 

    Tương tự như **Block Public Access settings for this bucket** ở mục *3.* phần [Thực Hành CÀI ĐẶT S3 STATIC WEB](https://thachpham2k.github.io/fcj-workshop1/vi/3-s3_staticweb/#th%E1%BB%B1c-h%C3%A0nh)

    ![Block public access settings for this bucket](/images/3-s3_staticweb/3.3-create.png)

    ```hcl
    resource "aws_s3_bucket_public_access_block" "bucket_public_access_block" {
        bucket = aws_s3_bucket.s3_bucket.id

        block_public_acls       = false
        block_public_policy     = false
        ignore_public_acls      = false
        restrict_public_buckets = false
    }
    ```

4. Bucket policy
    
    Phần này tương tự mục 8 đến 11 trong [Thực Hành CÀI ĐẶT S3 STATIC WEB](https://thachpham2k.github.io/fcj-workshop1/vi/3-s3_staticweb/#th%E1%BB%B1c-h%C3%A0nh)

    Để cấu hình chính sách cho bucket S3, chúng ta sử dụng resource `aws_s3_bucket_policy`. Trong phần này, chúng ta sử dụng một argument là `policy`, chúng ta sử dụng thông tin được lưu trữ trong tệp `s3_policy.json.tpl` bằng function `templatefile` và chuyển giá trị cho biến `bucket_name` bằng cách dưới đây. Ngoài ra, phần này sử dụng một meta-argument là `depends_on` để đảm bảo các resource cần thiết được tạo trước khi tới resource này.(không có dòng này lỗi đó 😁).

    ```hcl
    resource "aws_s3_bucket_policy" "bucket_policy" {
        bucket = aws_s3_bucket.s3_bucket.id

        policy = templatefile("${path.root}/${path.module}/s3_policy.json.tpl", {
            bucket_name = "${aws_s3_bucket.s3_bucket.bucket}"
        })
        depends_on = [ aws_s3_bucket.s3_bucket, aws_s3_bucket_ownership_controls.bucket_owner]
    }
    ```

5. Static Website Hosting

    Phần này tương đương với mục 4 đến 7 trong [Thực Hành CÀI ĐẶT S3 STATIC WEB](https://thachpham2k.github.io/fcj-workshop1/vi/3-s3_staticweb/#th%E1%BB%B1c-h%C3%A0nh)

    ![Static Website Hosting interface](/images/3-s3_staticweb/3.7-config.png)
    
    Để kích hoạt tính năng **Static Website Hosting** của S3 Bucket, chúng ta sử dụng resource `aws_s3_bucket_website_configuration` thông qua cấu hình dưới đây:
    
    ```hcl
    resource "aws_s3_bucket_website_configuration" "bucket_static_web_host" {
        bucket = aws_s3_bucket.s3_bucket.id

        index_document {
            suffix = "index.html"
        }
    }
    ```

    Ngoài ra sau khi khi kích hoạt tính năng này, chúng ta vẫn cần lấy giá trị **bucket website endpoint**, để lấy giá trị này, terraform cung cấp một attribute cho resource này là `website_endpoint` và chúng ta có thể lấy giá trị này thông qua **output block**:

    ```hcl
    output "webdomain" {
        value = aws_s3_bucket_website_configuration.bucket_static_web_host.website_endpoint
    }
    ```