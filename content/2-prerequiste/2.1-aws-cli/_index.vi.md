---
title : "Cài đặt AWS CLI"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.1. </b> "
---

## Tổng quan

{{% notice info %}}
AWS: Giao diện dòng lệnh AWS (AWS CLI) là công cụ thống nhất để quản lý các dịch vụ AWS của bạn. Chỉ với một công cụ để tải xuống và cấu hình, bạn có thể kiểm soát nhiều dịch vụ AWS bằng một dòng lệnh và tự động hóa chúng thông qua các tập lệnh.
{{% /notice %}}

> 👉 **Đơn giản thì**    
AWS Command Line Interface (CLI) là một công cụ cho phép bạn tương tác với AWS Service thông qua commandline trên terminal.

## Nội dung

Để cài đặt AWS CLI V2 thì các lệnh sau

```shell
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
# Remove install package
rm -rf awscliv2.zip aws/install
```

Đây là cách cài đặt AWS CLI trên linux nếu bạn cài trên nền tảng khác hoặc muốn tìm hiểu thêm về cách cài đặt hay cập nhật từ AWS Cli sang AWS Cli v2 thì có thể tham khảo thêm tại [đây](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)