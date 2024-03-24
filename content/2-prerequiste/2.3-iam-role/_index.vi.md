---
title : "Tạo IAM cho CodeBuild và CodePipeline"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.3. </b> "
---
## Tổng quan

{{% notice info %}}
Trong bước này chúng ta sẽ tiến hành tạo IAM Role. Trong IAM Role này sẽ được AWS CodeBuild và CodePipeline sử dụng.
{{% /notice %}}

## Thực hành
1. Truy cập vào giao diện [quản trị dịch vụ IAM](https://console.aws.amazon.com/iam/home)
1. Ở thanh điều hướng bên trái, chọn [Policies](https://console.aws.amazon.com/iam/home#/policies) 
1. Chọn Create Policies
	![IAM Policies interface](/$REPO_NAMEimages/2-prerequiste/2.3-iam-role/2.3.1-iam-policies.png)
1. Tại giao diện **Specify permissions** của **Create Policies**, chuyển qua tab json rồi dán đoạn json phía dưới vào sau đó chọn **next**

    ```json
	{
		"Version": "2012-10-17",
		"Statement": [
			{
				"Effect": "Allow",
				"Action": [
					"s3:*",
					"cloudwatch:*",
					"logs:*"
				],
				"Resource": "*"
			},
			{
				"Effect": "Allow",
				"Resource": [
					"*"
				],
				"Action": [
					"codecommit:*"
				]
			},
			{
				"Effect": "Allow",
				"Action": [
					"codebuild:*"
				],
				"Resource": "*"
			}
		]
	}
	```

	> 👉 **Tóm tắt json policy**    
	> -> Policy được khai báo gồm các quyền liên quan đến 
	> - S3 ("s3:\*"): Lưu artifact và gửi các file để deploy lên S3.
	> - Cloudwatch ("cloudwatch:\*"), Cloudwatch logs ("logs:\*"): lưu logs
	> - CodeCommit ("codecommit:\*"): Truy cập CodeCommit lấy source code để build và deploy.
	> - CodeBuild ("codebuild:\*"): Truy cập các hoạt động của CodeBuild.


1. Tại giao diện **Review and create** policies

   1. Nhập **Policy name**: `fcj-workshop`
   
   ![Name policy](/$REPO_NAMEimages/2-prerequiste/2.3-iam-role/2.3.2-iam-policies.png)
   
   2. Review lại thông tin 
   
   ![Review Policy](/$REPO_NAMEimages/2-prerequiste/2.3-iam-role/2.3.3-iam-policies.png)
   
   3. Đặt tags cho policy và chọn **Create policy**. (Code thể bỏ qua bước đặt tag nhưng với mình thì nên đặt tags để sau khi làm lab có thể dọn dẹp resource một cách hoàn toàn kẻo bị mất tiền *ngu*)
   
   ![Add tags and create policy](/$REPO_NAMEimages/2-prerequiste/2.3-iam-role/2.3.4-iam-policies.png)

3. Ở thanh điều hướng bên trái, chọn [Roles](https://console.aws.amazon.com/iam/home#/roles) 

5. Chọn **Create Role**

7. Ở giao diện **Select trusted entity** chọn `AWS Service` rồi chọn `CodeBuild` và chọn **Next**

	![alt text](/$REPO_NAMEimages/2-prerequiste/2.3-iam-role/2.3.5-iam-roles.png)

9. Trong phần **Add permissions** chọn `fcj-workshop` và **next**
	
	![Select create policy](/$REPO_NAMEimages/2-prerequiste/2.3-iam-role/2.3.6-iam-roles.png)

10. Ở giao diện **Name, review, and create**
    
	1. Nhập **Role name**: `fcj-workshop-role`
	
	![Name role](/$REPO_NAMEimages/2-prerequiste/2.3-iam-role/2.3.7-iam-roles.png)
	
	2. Đặt tags cho policy và chọn **Create Role**.
	
	![Add tags and create role](/$REPO_NAMEimages/2-prerequiste/2.3-iam-role/2.3.8-iam-roles.png)

11. Quay lại giao diện [Roles](https://console.aws.amazon.com/iam/home#/roles) Chọn Role vừa tạo (`fcj-workshop-role`)
	
	![Access created role](/$REPO_NAMEimages/2-prerequiste/2.3-iam-role/2.3.9-iam-roles.png)

12. Chọn tab `Trust relationships` rồi chọn `Edit trust policy`
	![Tab Trust relationships](/$REPO_NAMEimages/2-prerequiste/2.3-iam-role/2.3.10-iam-roles.png)

13. Tại giao diện **Edit trust policy** dán đoạn json sau:
	
	```json
	{
		"Version": "2012-10-17",
		"Statement": [
			{
				"Effect": "Allow",
				"Principal": {
					"Service": "codebuild.amazonaws.com"
				},
				"Action": "sts:AssumeRole"
			},
			{
				"Effect": "Allow",
				"Principal": {
					"Service": "codepipeline.amazonaws.com"
				},
				"Action": "sts:AssumeRole"
			}
		]
	}
	```
	
	> 👉 **Tóm tắt Json**   
	IAM Role lúc đầu chỉ có quyền gán cho CodeBuild (Bước số 8), nhưng mình muốn dùng 1 role cho 2 dịch vụ nên sẽ thêm `sts:AssumeRole` cho phép CodePipeline cũng sử dụng được role này.     

	Đoạn mà mình thêm:
	```json
	{
		"Effect": "Allow",
		"Principal": {
			"Service": "codepipeline.amazonaws.com"
		},
		"Action": "sts:AssumeRole"
	}
	```
	
	> ℹ️ Note:    
	Tuy nhiên, này là do làm lab, trong thực tế nên phân chia rõ ràng role dùng cho dịch vụ nào tránh để 1 role có thừa quyền (least privilege) và dễ dàng cho việc bảo trì.

	![Edit Trust policy](/$REPO_NAMEimages/2-prerequiste/2.3-iam-role/2.3.11-iam-roles.png)

14. Chọn **Update policy**