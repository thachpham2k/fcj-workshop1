---
title : "Cài đặt terraform"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2. </b> "
---

## Tổng quan

{{% notice info %}}
Terraform là một công cụ cơ sở hạ tầng dưới dạng mã mã nguồn mở do HashiCorp phát triển. Người dùng khai báo và cung cấp cơ sở hạ tầng trung tâm dữ liệu bằng ngôn ngữ cấu hình mang tính khai báo gọi là HashiCorp Configuration Language, hoặc có thể lựa chọn JSON
{{% /notice %}}

> 👉 **Đơn giản thì**    
Terraform is an IaC (infrastructure as code) tool 

## Nội dung

Để cài đặt terraform thì cần sử dụng các lệnh sau

```shell
# Cài đặt các công cụ cần thiết
sudo apt-get update -y
sudo apt-get install -y gnupg software-properties-common
# Cập nhật Hashicorp GDG key
wget -O- https://apt.releases.hashicorp.com/gpg | \
    gpg --dearmor | \
    sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
# Xác thực key
gpg --no-default-keyring \
    --keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
    --fingerprint
# Thêm HashiCorp repository vào repository của apt
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
    https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
    sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update
# Cài đặt
sudo apt-get install terraform
# Kiểm tra phiên bản của công cụ terraform vừa cài đặt
terraform -version
```

Đây là cách cài đặt terraform trên Ubuntu nếu bạn cài trên nền tảng khác hoặc muốn tìm hiểu thêm về cách cài đặt thì có thể tham khảo thêm tại [đây](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)