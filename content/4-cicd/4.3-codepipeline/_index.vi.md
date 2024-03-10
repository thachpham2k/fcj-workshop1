---
title : "Tạo CodePipeline"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 4.3. </b> "
---

## Tổng quan

{{% notice note %}}
AWS CodePipeline là dịch vụ phân phối liên tục được quản lý hoàn toàn giúp bạn tự động hóa các quy trình phát hành để cung cấp các bản cập nhật về ứng dụng và cơ sở hạ tầng một cách nhanh chóng và ổn định.
{{% /notice %}}

## Thực hành

1. Truy cập vào dịch vụ **AWS CodePipeline** hoặc có thể thông qua link [AWS CodePipeline](https://console.aws.amazon.com/codesuite/codepipeline/pipelines)

1. Chọn **Create pipeline**

1. Trong giao diện **Create pipeline**:
   
    - Ở Step 1: **Choose pipeline settings**
        - Mục **Pipeline name** điền `workshop-codepipeline`
        - Mục **Service role** chọn **Existing service role**
        - Mục **Role ARN** chọn ARN của role đã được tạo ở phần 2.3
        
        ![Choose pipeline settings](/fcj-workshop1/images/4-cicd/4.3-codepipeline/4.3.1-create.png)
        
        - Chọn **Next** để sang step 2

    - Ở Step 2: **Add source stage**
        - Mục **Source provider** chọn **AWS CodeCommit**
        - Mục **Repository name** điền `workshop-codecommit`
        - Mục **Branch name** chọn **master**

        ![Add source stage](/fcj-workshop1/images/4-cicd/4.3-codepipeline/4.3.2-create.png)

        - Chọn **Next**
    
    - Ở Step 3: **Add build stage**
        -  Mục **Build provider** chọn **AWS CodeBuild**
        -  Mục **Region** chọn region bạn đang sử dụng
        -  Mục **Project name** điền `workshop-codebuild`

        ![Add build stage](/fcj-workshop1/images/4-cicd/4.3-codepipeline/4.3.3-create.png)

        - Chọn **Next**

    - Ở Step 4: **Add deploy stage**
        - Mục **Deploy provider** chọn **Amazon S3**
        - Mục **Region** chọn region bạn đang sử dụng
        - Mục **Bucket** điền `workshop1-123` (tên của S3 bucket)
        - Chọn vào ô **Extract file before deploy** 

        ![Add deploy stage](/fcj-workshop1/images/4-cicd/4.3-codepipeline/4.3.4-create.png)

        - Chọn **Next**

    - Ở Step 5: **Review**: Nhìn tổng quan qua pipeline rồi chọn **Create pipeline**

1. Pipeline sau khi được tạo sử chạy thử 1 lần, nếu tất cả đều `succeded` thì có vẻ oke rồi á

    ![Pipeline first run](/fcj-workshop1/images/4-cicd/4.3-codepipeline/4.3.5-status.png)
    ![Pipeline first run (2)](/fcj-workshop1/images/4-cicd/4.3-codepipeline/4.3.6-status.png)
    ![Pipeline first run (3)](/fcj-workshop1/images/4-cicd/4.3-codepipeline/4.3.7-status.png)

1. Kiểm tra các tệp trong thư mục public đã được đẩy sang S3 bucket chưa

    ![S3 bucket check](/fcj-workshop1/images/4-cicd/4.3-codepipeline/4.3.8-s3-check.png)

1. Truy cập website bằng url ở phần 3 (S3 static website)

    ![S3 Website static hosting check](/fcj-workshop1/images/4-cicd/4.3-codepipeline/4.3.9-web-check.png)

{{% notice note %}}
Bạn có thể loại bỏ bước triển khai S3 trong codepipeline và sử dụng mã để đưa các tệp trong thư mục public sang S3 bucket. Tuy nhiên, tôi không thực hiện điều này vì tôi muốn phân biệt rõ ràng giữa bước xây dựng và bước triển khai.
{{% /notice %}}