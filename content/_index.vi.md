---
title : "Deploy Hugo Website with AWS CI/CD Tools"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
---
# Triển khai Hugo Website với bộ công cụ CICD của AWS

### Tổng quan

Trong bài viết này, chúng ta sẽ khám phá cách triển khai một trang web Hugo với Continuous Integration/Continuous Deployment (CI/CD). Tìm hiểu thêm về một số khái niệm quan trọng liên quan đến quá trình triển khai trang web tĩnh với S3 và ba công cụ CI/CD phổ biến của AWS là AWS CodePipeline, AWS CodeCommit và AWS CodeBuild. Không chỉ áp dụng cho việc triển khai trang web Hugo, bạn cũng hoàn toàn có thể sử dụng cách tiếp cận này để triển khai các trang web front-end khác như react, Vue, và nhiều hơn nữa.

Ngoài việc triển khai bằng Console, chúng ta cũng sẽ kết hợp với Terraform để xây dựng bài lab một cách tự động. Điều này sẽ giúp tối ưu hóa quy trình triển khai và quản lý hạ tầng một cách hiệu quả.

![Diagram](/fcj-workshop1/images/diagram.png)

### Nội dung

{{% children  %}}
