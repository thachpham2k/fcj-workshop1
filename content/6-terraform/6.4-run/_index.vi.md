---
title : "Quản lý tài nguyên với Terraform"
menuTitle : "Terraform Manage"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 6.4. </b> "
description: "Chạy các lệnh Terraform để quản lý tài nguyên trên nền tảng AWS, từ khởi tạo đến xóa dọn."
---

## Tổng quan

Trong phần này, chúng ta sẽ thực hiện các lệnh Terraform để tạo và quản lý các tài nguyên trên AWS.

## Thực hiện

Đầu tiên, hãy mở terminal và di chuyển đến thư mục chứa các file cấu hình Terraform.

```shell
cd path/to/terraform/files
```

Tiếp theo, chạy các lệnh sau:

```shell
terraform init
```

Lệnh này sẽ khởi tạo Terraform và tải về các plugins cần thiết cho các provider đã được khai báo trong các file cấu hình.

```shell
terraform plan
```

Lệnh này sẽ kiểm tra cấu hình và hiển thị các thay đổi dự kiến sẽ được áp dụng khi chạy `terraform apply`. 

```shell
terraform apply -auto-approve
```

Lệnh này sẽ thực hiện áp dụng các thay đổi vào hạ tầng AWS một cách tự động và không cần xác nhận từ người dùng. 

## Kiểm tra kết quả

Sau khi thực hiện lệnh `terraform apply`, hãy kiểm tra lại trên giao diện của AWS để đảm bảo rằng các tài nguyên đã được tạo và cấu hình đúng như mong đợi.

Chạy Apply command thành công:

![Alt text](/$REPO_NAMEimages/6-terraform/6.4-run/6.4.1-apply.png)

Kiểm tra S3:

![Alt text](/$REPO_NAMEimages/6-terraform/6.4-run/6.4.2-s3.png)

Kiểm tra CodeCommit:

![Alt text](/$REPO_NAMEimages/6-terraform/6.4-run/6.4.3-codecommit.png)

Kiểm tra CodeBuild:

![Alt text](/$REPO_NAMEimages/6-terraform/6.4-run/6.4.4-codebuild.png)

Kiểm tra CodePipeline:

![Alt text](/$REPO_NAMEimages/6-terraform/6.4-run/6.4.5-codepipeline.png)

Truy cập trang web bằng S3 Static Web URL:

![Alt text](/$REPO_NAMEimages/6-terraform/6.4-run/6.4.6-website.png)

## Dọn dẹp

Khi bạn đã hoàn thành công việc và muốn xóa các tài nguyên đã tạo, bạn có thể chạy lệnh sau:

```shell
terraform destroy -auto-approve
```

Lệnh này sẽ xóa tất cả các tài nguyên đã tạo bằng Terraform một cách tự động và không cần xác nhận từ người dùng.