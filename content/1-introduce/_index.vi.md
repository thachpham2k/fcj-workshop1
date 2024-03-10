---
title : "Giới thiệu"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---

**Hugo** là một hệ thống tạo trang web tĩnh và mã nguồn mở, được xây dựng bằng ngôn ngữ lập trình Go. Nó cho phép người dùng tạo ra các trang web tĩnh với tốc độ nhanh và dễ dàng quản lý. Đặc biệt, Hugo sử dụng cấu trúc tệp tin đơn giản và nhanh chóng để tạo ra các trang web với nhiều chủ đề và tùy chọn linh hoạt.

Để triển khai một trang web Hugo sử dụng AWS, chúng ta có thể sử dụng các dịch vụ của AWS như **CodeCommit**, **CodeBuild**, **CodePipeline** và **S3 Static Website Hosting**. Cụ thể:
- **CodeCommit**: Sử dụng để lưu trữ mã nguồn của trang web Hugo. Người dùng có thể tạo một kho mã nguồn trên CodeCommit và đẩy mã nguồn của dự án Hugo lên đó.
- **CodeBuild**: Được sử dụng để xây dựng trang web Hugo. Một tệp buildspec.yml sẽ được tạo để chỉ định các bước cần thực hiện trong quá trình xây dựng, bao gồm việc cài đặt Hugo và xây dựng trang web từ mã nguồn.
- **CodePipeline**: Sử dụng để tự động hóa quá trình triển khai trang web Hugo. Một pipeline sẽ được tạo, kết hợp các bước từ CodeCommit (nguồn), CodeBuild (xây dựng) và triển khai lên S3 (hosting).
- **S3 Static Website Hosting**: Sử dụng để lưu trữ và phục vụ trang web Hugo dưới dạng tĩnh. Khi quá trình triển khai hoàn tất, các tệp tin của trang web Hugo sẽ được đẩy lên S3 bucket và trở thành trang web tĩnh có thể truy cập từ Internet.

Tóm lại, việc triển khai trang web Hugo bằng cách sử dụng các dịch vụ của AWS mang lại sự tự động hóa và hiệu suất cao trong quá trình phát triển và triển khai trang web.