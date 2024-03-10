---
title : "Tạo CodeCommit để lưu trữ mã nguồn"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 4.1. </b> "
---
## Tổng quan

AWS CodeCommit là một dịch vụ quản lý mã nguồn được quản lý hoàn toàn, giúp các nhóm dễ dàng hợp tác an toàn trên mã nguồn trong các kho Git riêng tư.
- CodeCommit cho phép các nhà phát triển lưu trữ mã nguồn trong các kho Git riêng tư và đồng thời hợp tác trên các thay đổi mã nguồn với đồng nghiệp và đóng góp từ bên ngoài.
- Nó tích hợp với các dịch vụ AWS khác như CodePipeline để kích hoạt các luồng công việc CI/CD trực tiếp từ một kho CodeCommit.
- Các lệnh và công cụ Git thông thường có thể được sử dụng để làm việc với các kho CodeCommit, loại bỏ nhu cầu quản lý cơ sở hạ tầng quản lý mã nguồn của riêng bạn.

## Thực hành

1. Truy cập vào dịch vụ AWS CodeCommit hoặc có thể thông qua link [AWS CodeCommit](https://console.aws.amazon.com/codesuite/codecommit/repositories)

1. Chọn **Create Repository**

1. Trong giao diện **Create Repository** 
   
    - Tại mục **Repository name** điền `workhsop-codecommit`
    ![Repository settings](/fcj-workshop1/images/4-cicd/4.1-codecommit/4.1.1-create.png)

    - Kéo xuống cuối trang chọn **Create**

1. Trong giao diện **CodeCommit Repositories** chọn vào repository vừa tạo (`workshop-codecommit`). Ở phần **Connection Steps** lưu giá trị repository url lại

    ![Connection Steps](/fcj-workshop1/images/4-cicd/4.1-codecommit/4.1.2-connection.png)

1. Đăng nhập vào AWS Cli và git
   
    > 👉 Để có thể đẩy mã nguồn lên CodeCommit, trước tiên bạn cần thêm các thông tin xác thực cho CodeCommit vào Git sử dụng AWS Credentials.
    
    1. Thêm AWS Credentials vào Terminal: Sử dụng aws token và lệnh `aws configure` để xác thực AWS Cli. Nếu bạn chưa biết bạn có thể tham khảo bài hướng dẫn [Authenticate with IAM user credentials](https://docs.aws.amazon.com/cli/latest/userguide/cli-authentication-user.html).
   2. Đăng nhập vào Git và cấu hình: Tiếp theo, bạn cần đăng nhập vào Git và cấu hình bằng cách thực hiện các bước sau:

    ```shell
    # Login to aws cli
    aws configure

    # Configure Git to use aws codecommit credential-helper
    git config --global credential.helper '!aws codecommit credential-helper $@'
    # Enable using HTTP path for Git
    git config --global credential.UseHttpPath true
    ```
    ![Config aws cli and git](/fcj-workshop1/images/4-cicd/4.1-codecommit/4.1.3-cli-config.png)

1. Mã nguồn hugo project {{% button href="/source/000000-Workshop.zip" icon="fas fa-download" icon-position="right" %}}Download Source{{% /button %}}

1. Cuối cùng, đẩy mã nguồn lên CodeCommit bằng cách thực hiện các bước sau

    ```shell
    git init
    git remote add origin https://git-codecommit.ap-southeast-1.amazonaws.com/v1/repos/workshop-codecommit
    git add .
    git commit -m "first commit"
    git push --set-upstream origin master
    ```

    Lưu ý: https://git-codecommit.ap-southeast-1.amazonaws.com/v1/repos/workshop-codecommit là đường dẫn đến codecommit repo là url được lấy ở bước 4 (Trong phần Connection Steps)
  
    ![Push code to AWS CodeCommit](/fcj-workshop1/images/4-cicd/4.1-codecommit/4.1.4-git-commit.png)
  
    ![Push code to AWS CodeCommit (2)](/fcj-workshop1/images/4-cicd/4.1-codecommit/4.1.5-git-commit.png)

1. Khi đã hoàn thành, mã nguồn của bạn đã được đẩy thành công lên AWS CodeCommit.

![Push sourcecode success](/fcj-workshop1/images/4-cicd/4.1-codecommit/4.1.6-commit-success.png)

## Tài liệu đính kèm

{{%attachments style="blue" /%}}