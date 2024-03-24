---
title : "Tạo Root Module"
menuTitle : "Module root"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 6.3. </b> "
description: "Khai báo và sử dụng các module đã tạo ở các bước trước đó."
---

## Tổng quan

Trong phần này, chúng ta sẽ khai báo và sử dụng các module đã tạo ở các bước trước đó. Mục đích của việc sử dụng module là để tái sử dụng mã nguồn và tạo ra sự cấu trúc tổ chức và quản lý mã nguồn dễ dàng hơn.

## Thực hiện

Khai báo Modules

Trong file root module, chúng ta sẽ khai báo sử dụng hai module đã tạo trước đó: `staticweb` và `cicd`. Dưới đây là cách khai báo các module này:

```hcl
module "s3" {
    source        = "./modules/staticweb"
    project-name  = var.project-name
    project-tags  = var.project-tags
}

module "codepipeline" {
    source        = "./modules/cicd"
    s3_bucket     = module.s3.s3-bucket
    project_name  = var.project-name
    repo_src_path = "src"
    project_tags  = var.project-tags
}
```

Trong đoạn mã trên:

- Module `s3` được sử dụng để tạo và cấu hình S3 static website. Chúng ta cung cấp các biến `project-name` và `project-tags` cho module này.
- Module `codepipeline` được sử dụng để tạo và cấu hình AWS Pipeline để tự động xây dựng và triển khai website. Chúng ta cung cấp các biến `s3_bucket`, `project_name`, `repo_src_path`, và `project_tags` cho module này.

Việc này giúp chúng ta tổ chức mã nguồn một cách có cấu trúc hơn và dễ dàng quản lý các thành phần của dự án.