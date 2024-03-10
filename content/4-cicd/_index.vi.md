---
title : "Thiết lập CICD"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

Trong giai đoạn này, chúng ta sẽ thiết lập một Pipeline để tự động xây dựng và triển khai dự án. Chúng ta sẽ sử dụng các dịch vụ của AWS như:
- AWS CodeCommit để lưu trữ mã nguồn
- AWS CodeBuild để thực hiện quá trình xây dựng và tạo các tập tin public
- AWS CodePipeline để quản lý toàn bộ quy trình và đưa các tập tin trong thư mục public lên S3 bucket đã được tạo sẵn.

### Content:

{{% children  %}}