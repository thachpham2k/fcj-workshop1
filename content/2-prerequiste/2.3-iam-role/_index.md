---
title : "Create IAM Role for CodeBuild and CodePipeline"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.3. </b> "
---
## Overview

{{% notice info %}}
In this step, we will create an IAM Role. This IAM Role will be used by AWS CodeBuild and CodePipeline.
{{% /notice %}}

## Content

1. Access [IAM](https://console.aws.amazon.com/iam/home)

1. On the left navigation bar, select [Policies](https://console.aws.amazon.com/iam/home#/policies) 

1. Choose Create Policies.
    
    ![IAM Policies interface](/images/2-prerequiste/2.3-iam-role/2.3.1-iam-policies.png)

1. On the **Specify permissions** interface of **Create Policies**, switch to the json tab, then paste the following json snippet, and select **next**

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

	> 👉 **Json Policy Summary**    
	> -> The policy declares permissions related to: 
	> - S3 ("s3:\*"): Storing artifacts and sending files to deploy to S3.
	> - Cloudwatch ("cloudwatch:\*"), Cloudwatch logs ("logs:\*"): Logging.
	> - CodeCommit ("codecommit:\*"): Access CodeCommit to get source code for building and deploying.
	> - CodeBuild ("codebuild:\*"): Access CodeBuild operations.


1. On the **Review and create** interface

   1. Enter **Policy name**: `fcj-workshop`
   
   ![Name policy](/images/2-prerequiste/2.3-iam-role/2.3.2-iam-policies.png)
   
   2. Review information
   
   ![Review Policy](/images/2-prerequiste/2.3-iam-role/2.3.3-iam-policies.png)
   
   3. Tag the policy and select **Create policy**. (You can skip adding tags, but I prefer to add tags to clean up resources completely after the lab)
   
   ![Add tags and create policy](/images/2-prerequiste/2.3-iam-role/2.3.4-iam-policies.png)

3. On the left navigation bar, select [Roles](https://console.aws.amazon.com/iam/home#/roles) 

5. Choose **Create Role**

7. On the **Select trusted entity**, choose `AWS Service`, then select `CodeBuild` và click **Next**

	![Select trusted entity](/images/2-prerequiste/2.3-iam-role/2.3.5-iam-roles.png)

9. In **Add permissions** section, choose `fcj-workshop` and click **next**
	
	![Select create policy](/images/2-prerequiste/2.3-iam-role/2.3.6-iam-roles.png)

10. On the **Name, review, and create**
    
	1. Enter **Role name**: `fcj-workshop-role`
	
	![Name role](/images/2-prerequiste/2.3-iam-role/2.3.7-iam-roles.png)
	
	2. Tag the role and select **Create Role**.
	
	![Add tags and create role](/images/2-prerequiste/2.3-iam-role/2.3.8-iam-roles.png)

11. Go back to the [Roles](https://console.aws.amazon.com/iam/home#/roles), Select the created Role (`fcj-workshop-role`)
	
	![Access created role](/images/2-prerequiste/2.3-iam-role/2.3.9-iam-roles.png)

12. Click on tab `Trust relationships` then select `Edit trust policy`
	![Tab Trust relationships](/images/2-prerequiste/2.3-iam-role/2.3.10-iam-roles.png)

13. On the **Edit trust policy**, paste the following json:
	
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
	
	> 👉 **Brief**   
	IAM Role initially only had permissions assigned to CodeBuild (Step 8), but since I want to use one role for two services, I will add sts:AssumeRole to allow CodePipeline to also use this role. 

	In this json, I add:
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
	However, this is for lab purposes; in practice, roles should be clearly divided for which service to avoid having a role with unnecessary privileges (least privilege) and for easy maintenance.

	![Edit Trust policy](/images/2-prerequiste/2.3-iam-role/2.3.11-iam-roles.png)

14. Choose **Update policy**