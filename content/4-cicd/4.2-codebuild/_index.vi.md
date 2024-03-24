---
title : "Tạo CodeBuild"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 4.2. </b> "
---

## Tổng quan

{{% notice note %}}
**AWS CodeBuild** là dịch vụ tích hợp liên tục được quản lý hoàn toàn giúp biên dịch mã nguồn, chạy kiểm thử và sản xuất các gói phần mềm sẵn sàng triển khai.
{{% /notice %}}

Một số tính năng quan trọng của AWS CodeBuild bao gồm:
- Nó cho phép bạn sử dụng môi trường xây dựng được đóng gói sẵn do AWS cung cấp như Ubuntu, Windows, v.v. hoặc sử dụng môi trường xây dựng tùy chỉnh với các công cụ xây dựng riêng của bạn.
- CodeBuild tự động mở rộng lên và xuống để xử lý nhiều quá trình xây dựng đồng thời, giúp quá trình xây dựng không phải chờ đợi trong hàng đợi.
- Bạn chỉ trả phí cho các tài nguyên tính toán được sử dụng trong quá trình xây dựng của bạn, vì vậy không có chi phí ban đầu hoặc cần phải cấu hình máy chủ.
- Để bắt đầu với CodeBuild, bạn tạo một dự án xây dựng mà chỉ định vị trí mã nguồn, môi trường xây dựng để sử dụng và các lệnh xây dựng.
- Khi một dự án được tạo, bạn có thể kích hoạt các quá trình xây dựng một cách thủ công hoặc thiết lập tích hợp liên tục bằng cách tích hợp với các kho mã như GitHub.
- Các nhật ký và các tài liệu xây dựng được lưu trữ để bạn có thể gỡ lỗi các vấn đề hoặc triển khai các gói sau một quá trình xây dựng thành công.


## Thực hành

1. Tạo tệp `buildspec.yml` trong mã nguồn

    Tệp Buildspec là một tập tin cấu hình dùng để chỉ định các bước cần thực hiện trong quá trình xây dựng (build) khi sử dụng dịch vụ AWS CodeBuild.

    Nội dung tệp `buildspec.yml`

    ```yml
    version: 0.2
    phases:
      install:
        commands:
          - apt-get update -y
          - echo Installing hugo
          - curl -L -o hugo.deb https://github.com/gohugoio/hugo/releases/download/v0.123.7/hugo_0.123.7_linux-amd64.deb
          - dpkg -i hugo.deb
          - hugo version
      build:
        commands:
          - hugo -v
    artifacts:
      files:
        - '**/*'
      base-directory: 'public'
    ```

1. Đẩy mã nguồn mới chứa tệp buildspec.yml lên CodeCommit

    Sau khi tạo tệp buildspec.yml, sử dụng các lệnh sau để đẩy mã nguồn lên CodeCommit

    ```shell
    git add .
    git commit -m "add buildspec.yml file"
    git push
    ```

    ![Push buildspec.yml file to Codecommit](/$REPO_NAMEimages/4-cicd/4.2-codebuild/4.2.1-add-buildspec.png)

1. Truy cập vào dịch vụ **AWS CodeBuild** hoặc có thể thông qua link [AWS CodeBuild](https://console.aws.amazon.com/codesuite/codebuild/projects)

1. Chọn **Create project**

1. Trong giao diện **Create project**:

     - Phần Project configuration: Tại mục **project name** điền `workshop-codebuild`
     
       ![Project configuration](/$REPO_NAMEimages/4-cicd/4.2-codebuild/4.2.2-create.png)

     - Phần Source:
       - Mục **Source provider** chọn **AWS CodeCommit**
       - Mục **repository** điền `workshop-codecommit`
       - Mục **reference type** chọn **Branch**
       - Mục **Branch** chọn **master**
       
       ![Source](/$REPO_NAMEimages/4-cicd/4.2-codebuild/4.2.3-create.png)

     - Phần Environment:
       - Mục **Compute** chọn **EC2**
       - Mục **Operating system** chọn **Ubuntu**
       - Mục **Runtime(s)** chọn **Standard**
       - Mục **Image** chọn **aws/codebuild/standard:7.0**
       - Mục **Service role** chọn **Existing service role**
       - Mục **Role ARN** chọn ARN của role đã được tạo ở phần 2.3
       
       ![Environment](/$REPO_NAMEimages/4-cicd/4.2-codebuild/4.2.4-create.png)
     
     - Phần Buildspec:
       - Mục **Build specifications** chọn **Use a buildspec file**
       - Mục **Buildspec name** điền `buildspec.yml` (điền vị trí lưu file buildspec được tạo ở bước 1)

       ![Buildspec](/$REPO_NAMEimages/4-cicd/4.2-codebuild/4.2.5-create.png)

1. Nhấn vào nút **Create Build project** ở cuối trang

1. Sau khi tạo build project thì có thể chạy thử bằng cách nhấn vào nút **Start build** màu cam bên phải. Kết quả của quá trình xây dựng sẽ được hiển thị ở tab **Build history**

  ![Test build](/$REPO_NAMEimages/4-cicd/4.2-codebuild/4.2.6-build.png)
