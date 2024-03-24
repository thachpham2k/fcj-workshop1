---
title: "Triển khai bằng Terraform"
menuTitle: "Terraform"
date: "`r Sys.Date()`" 
weight: 6
chapter: false
pre: "<b> 6. </b>"
---

## Tổng quan

**Terraform** là một công cụ mã nguồn mở được sử dụng để quản lý cơ sở hạ tầng ở các môi trường cloud và on-premises. Nó cho phép người dùng xây dựng, thay đổi và quản lý cơ sở hạ tầng phần mềm sử dụng cú pháp đơn giản và dễ đọc.

Cấu trúc cơ bản của Terraform mà chúng ta sẽ sử dụng trong bài:
- **Block**: Đây là thành phần cơ bản nhất của cấu hình Terraform, mỗi block đại diện cho một phần của cấu hình như resource, output, module, ...
  Ngoài các block chính thì chúng ta còn sử dụng một loại khác là nested block (block bên trong block).
- **Argument**: Được sử dụng để cấu hình các thuộc tính của các block.
  Một loại argument đặc biệt có thể sử dụng ở nhiều loại resource nhưng lại có chung 1 cơ chế hoạt động gọi **meta-argument** như `count`, `for_each`,...
- **Attribute**: Được sử dụng để truy cập các giá trị của các resource sau khi chúng được tạo.

**Module**: Để dễ quản lý, chúng ta sẽ chia bài lab ra thành 3 module và được chia thành 2 loại:
- **Root Module**: Là module chính chứa toàn bộ cấu hình Terraform.
- **Child Module**: Các module con được sử dụng trong root module để tách riêng các phần của cấu hình. Trong phần này, chúng ta sẽ xây dựng 2 child module là:
  - Static web module: Tạo S3 và cấu hình static website.
  - Cicd module: Phục vụ cho quá trình lưu trữ và triển khai mã nguồn.

Bạn có thể tham khảo source code của Terraform tại [đây](https://github.com/thachpham2k/fcj-workshop1-terraform).

## Nội dung

{{% children description="true" %}}

## Tổng kết

Sau bài lab mong bạn có thể rút ra được kết luận về 2 cách triển khai.
