---
title : "Dọn dẹp tài nguyên"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : "<b>5. </b>"
---

Chúng ta sẽ tiến hành các bước sau để xóa các tài nguyên chúng ta đã tạo trong bài thực hành này.

## Xóa CodePipeline

1. Truy cập vào dịch vụ [AWS CodePipeline](https://console.aws.amazon.com/codesuite/codepipeline/pipelines)

1. Chọn vào Pipeline chúng ta đã tạo. (Ví dụ `workshop-codepipeline`)

    - Chọn **Delete pipeline**
  
    ![Select pipeline](/fcj-workshop1/images/5-cleanup/5.1-codepipeline.png)

    - Điền `delete` sau đó chọn **Delete**

    ![Delete pipeline](/fcj-workshop1/images/5-cleanup/5.2-codepipeline.png)

## Xóa CodeBuild

1. Truy cập vào dịch vụ [AWS CodeBuild](https://console.aws.amazon.com/codesuite/codebuild/projects)

1. Chọn vào build project chúng ta đã tạo. (ví dụ: `workshop-codebuild`)

    - Chọn **Action** sau đó chọn **Delete**

    ![Select](/fcj-workshop1/images/5-cleanup/5.3-codebuild.png)

    - Điền `delete` sau đó chọn **Delete**

    ![Delete](/fcj-workshop1/images/5-cleanup/5.4-codebuild.png)

## Xóa CodeCommit

1. Truy cập vào dịch vụ [AWS CodeCommit](https://console.aws.amazon.com/codesuite/codecommit/repositories)

1. Chọn vào repository chúng ta đã tạo. (ví dụ: `workshop-codecommit`)

    - Chọn **Delete repository**

    ![Select repository](/fcj-workshop1/images/5-cleanup/5.5-codecommit.png)

    - Điền `delete` sau đó chọn **Delete**

## Xóa S3

1. Truy cập [giao diện quản trị dịch vụ S3](https://s3.console.aws.amazon.com/s3/home)
    -  Click chọn S3 bucket chúng ta đã tạo cho bài thực hành. ( Ví dụ : `workshop1-123`)
    - Click **Empty**.
    
    ![Select bucket](/fcj-workshop1/images/5-cleanup/5.6-s3-website.png)

    - Điền **permanently delete**, sau đó click **Empty** để tiến hành xóa toàn bộ objects trong bucket.
    
    ![Empty bucket](/fcj-workshop1/images/5-cleanup/5.7-s3-website.png)

    - Click **Exit**.

1. Sau khi xóa hết object trong bucket, click **Delete**

    ![Select bucket](/fcj-workshop1/images/5-cleanup/5.8-s3-website.png)

1. Điền tên S3 bucket (`workshop1-123`), sau đó click **Delete bucket** để tiến hành xóa S3 bucket.

    ![Delete bucket](/fcj-workshop1/images/5-cleanup/5.9-s3-website.png)

1. Ngoài S3 bucket được chúng ta tạo còn có một bucket khác do **AWS Codepipeline** sử dụng để lưu artifact trong quá trình thực thi pipeline có tên dạng `codepipeline-<region>-*` (ví dụ: `codepipeline-ap-southeast-1-661747411738`)

2. Truy cập vào Bucket bằng cách chọn vào tên bucket.

    - Tìm đến Object `workshop-codepipline/` (tên của pipeline được tạo trong phần AWS CodePipeline)

    ![Bucket](/fcj-workshop1/images/5-cleanup/5.10-s3-pipeline.png)

    - Điền **permanently delete**, sau đó click **Delete Objects** để tiến hành xóa object trong bucket.

    ![Delete Object](/fcj-workshop1/images/5-cleanup/5.11-s3-pipeline.png)

## Xóa IAM Role

1. Truy cập [giao diện quản trị dịch vụ IAM](https://console.aws.amazon.com/iamv2/home#/home)

    - Click **Roles**.
    - Tại ô tìm kiếm , điền `fcj-workshop-role`.
    - Click chọn **fcj-workshop-role**.
    - Click **Delete**, sau đó điền tên role `fcj-workshop-role` và click **Delete** để xóa role.

    ![IAM Roles](/fcj-workshop1/images/5-cleanup/5.12-role.png)

## Xóa IAM Policy

1. Truy cập [giao diện quản trị dịch vụ IAM](https://console.aws.amazon.com/iamv2/home#/home)

    - Click **Policies**.
    - Tại ô tìm kiếm , điền `fcj-workshop`.
    - Click chọn **fcj-workshop**.
    - Click **Delete**, sau đó điền tên role `fcj-workshop` và click **Delete** để xóa role.

    ![IAM Policies](/fcj-workshop1/images/5-cleanup/5.13-policy.png)
