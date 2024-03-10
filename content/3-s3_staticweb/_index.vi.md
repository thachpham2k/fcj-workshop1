---
title : "Cài đặt S3 Static Web"
date :  "`r Sys.Date()`" 
weight : 3 
chapter : false
pre : " <b> 3. </b> "
---

## Tổng quan

Bước đầu tiên vẫn là tạo trang web trước, nội dung tính sau 😂.Để host trang web tĩnh, AWS có cung cấp tính năng Static website hosting dựa trên Amazon S3.

<details>
<summary>
<b>Amazon S3 (Simple Storage Service)</b>: là dịch vụ lưu trữ đối tượng được xây dựng để truy xuất dữ liệu thuộc mọi kích thước và từ mọi nơi.
</summary>
    
Một số điều cần biết về S3:
- S3 cung cấp lưu trữ cực kỳ bền vững cho tất cả các loại dữ liệu bao gồm văn bản, hình ảnh, video, v.v. Dữ liệu được lưu trữ trên nhiều thiết bị và cơ sở và được thiết kế để chịu được mất mát của 2 cơ sở cùng một lúc.
- Các đối tượng được lưu trữ và truy xuất thông qua một REST API đơn giản. Cách phổ biến để giao tiếp với S3 bao gồm AWS CLI, SDK cho các ngôn ngữ khác nhau như Java, Python, v.v. và bảng điều khiển S3.
- Buckets được sử dụng để tổ chức các đối tượng trong S3. Bạn có thể tạo nhiều bucket công cộng hoặc riêng tư để phân loại dữ liệu của bạn.
- Kiểm soát truy cập được phân đoạn xuống cấp độ đối tượng cá nhân. Bạn có thể thiết lập quyền truy cập, đọc hoặc ghi cho từng đối tượng bằng cách sử dụng chính sách và vai trò IAM.
- Các trường hợp sử dụng phổ biến cho S3 bao gồm phục vụ các trang web tĩnh, lưu trữ dữ liệu ứng dụng và bản sao lưu, lưu trữ dữ liệu dài hạn và trao đổi dữ liệu giữa các ứng dụng.
</details>

## Thực hành

1. Truy cập vào dịch vụ AWS S3 trên console hoặc có thể thông qua link [AWS S3](https://s3.console.aws.amazon.com/s3/home)

1. Chọn **Create Bucket** bên phải.

1. Trong giao diện Create bucket
    
    - Region: `ap-southeast-1`
    - Bucket name: `workshop1-123`
    
    > 💭 **Mẹo**   
    Vì tên của bucket bắt buộc phải là duy nhất nên thường mình sẽ thêm 1 dãy số phía sau tên mong muốn.
    
    
    ![General configuration](/fcj-workshop1/images/3-s3_staticweb/3.1-create.png)

    - Object Ownership: `ACLs enabled`
    
    ![Object ownership](/fcj-workshop1/images/3-s3_staticweb/3.2-create.png)

    - Tắt tính năng **Block all public access**
    - Chọn vào `I acknowledge that the current settings might result in this bucket and the objects within becoming public.` trong cảnh báo **Turning off block all public access might result in this bucket and the objects within becoming public**
    
    ![Block public access settings for this bucket](/fcj-workshop1/images/3-s3_staticweb/3.3-create.png)

    - Cuối cùng là chọn `Create Bucket`

   -> Bucket `workshop1-123` đã được tạo thành công. 
   
1. Tính năng **S3 static hosting** nằm ở tab **Properties** của bucket. Theo như lúc mình làm thì sẽ chọn tab **Properties** sau đó tìm đến **Static website hosting** ở cuối của tab. 

    ![Select Properties tab](/fcj-workshop1/images/3-s3_staticweb/3.5-config.png)

1. Chọn vào **Edit** của tính năng **Static website hosting**
 
    ![Edit Static Website Hosting feature](/fcj-workshop1/images/3-s3_staticweb/3.6-config.png)

1. Ở trang **Edit static website hosting**:

   1. **Static website hosting**: `enable`
   1. **Index document**: `index.html`
   
        ![Static Website Hosting interface](/fcj-workshop1/images/3-s3_staticweb/3.7-config.png)

    1. Chọn **Save changes**

1. Quanh trở lại tính năng **Static website hosting**, giao diện hiển thị đã thay đổi. (Lúc này lưu giá trị **bucket website endpoint** lại để sau này sử dụng)

    ![Enable Static Website Hosting success](/fcj-workshop1/images/3-s3_staticweb/3.8-config.png)

    > ℹ️  **Note**     
    > Lúc này S3 website endpoint này có thể xem là vẫn chưa hoạt động vì:
    > - Trang web không có file (`index.html`) để hiển thị.
    > - Nếu có file thì người dùng vẫn không có quyền để truy cập file trong S3.    
    > -> Bước tiếp theo sẽ tiến hành cấu hình policy để người dùng có quyền truy cập vào nội dung file bên trong S3.    

    ![Access S3 static website using http](/fcj-workshop1/images/3-s3_staticweb/3.9-check.png)

1. Tìm đến Tab **Permissions**
1. Tìm đến phần **Bucket policy**, chọn **Edit**

    ![Bucket Policy](/fcj-workshop1/images/3-s3_staticweb/3.10-policy.png)

1. Dán đoạn json policy phía dưới vào.

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
                    "arn:aws:s3:::workshop1-123/*"
                ]
            }
        ]
    }
    ```

    > 👉 Tóm tắt Json    
    Policy JSON này cấu hình cho phép tất cả các người dùng (Principal) được phép thực hiện hành động (Action) là `s3:GetObject` trên các đối tượng trong bucket có tên là `workshop1-123`. Điều này có nghĩa là mọi người, bao gồm cả người dùng không xác định, đều có quyền truy cập và đọc (GetObject) các đối tượng trong bucket này.

4. Chọn **Save changes**

-> Đã hoàn thành việc tạo và cấu hình 1 website sử dụng tính năng **Static website hosting** của S3.
