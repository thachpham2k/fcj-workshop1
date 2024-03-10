---
title : "Create CodePipeline"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 4.3. </b> "
---

## Overview

{{% notice note %}}
AWS CodePipeline is a fully managed continuous delivery service that helps automate the release process to deliver updates to your application and infrastructure quickly and reliably.
{{% /notice %}}

## Practice

1. Access the AWS CodePipeline service or through the link [AWS CodePipeline](https://console.aws.amazon.com/codesuite/codepipeline/pipelines)

2. Choose **Create pipeline**

3. In the **Create pipeline** interface:
   
    - Step 1: **Choose pipeline settings**
        - For **Pipeline name**, enter `workshop-codepipeline`
        - For **Service role**, select **Existing service role**
        - For **Role ARN**, choose the ARN of the role created in step 2.3
        
        ![Choose pipeline settings](/images/4-cicd/4.3-codepipeline/4.3.1-create.png)
        
        - Click **Next** to proceed to step 2

    - Step 2: **Add source stage**
        - For **Source provider**, select **AWS CodeCommit**
        - For **Repository name**, enter `workshop-codecommit`
        - For **Branch name**, choose **master**

        ![Add source stage](/images/4-cicd/4.3-codepipeline/4.3.2-create.png)

        - Click **Next**
    
    - Step 3: **Add build stage**
        - For **Build provider**, select **AWS CodeBuild**
        - For **Region**, choose the region you are using
        - For **Project name**, enter `workshop-codebuild`

        ![Add build stage](/images/4-cicd/4.3-codepipeline/4.3.3-create.png)

        - Click **Next**

    - Step 4: **Add deploy stage**
        - For **Deploy provider**, select **Amazon S3**
        - For **Region**, choose the region you are using
        - For **Bucket**, enter `workshop1-123` (the name of the S3 bucket)
        - Check the **Extract file before deploy** box

        ![Add deploy stage](/images/4-cicd/4.3-codepipeline/4.3.4-create.png)

        - Click **Next**

    - Step 5: **Review**: Review the pipeline overview and then click **Create pipeline**

4. Once the pipeline is created, it will run a test run once. If all steps are `succeded`, then it seems okay.

    ![Pipeline first run](/images/4-cicd/4.3-codepipeline/4.3.5-status.png)
    ![Pipeline first run (2)](/images/4-cicd/4.3-codepipeline/4.3.6-status.png)
    ![Pipeline first run (3)](/images/4-cicd/4.3-codepipeline/4.3.7-status.png)

5. Check if the files in the public directory have been pushed to the S3 bucket or not

    ![S3 bucket check](/images/4-cicd/4.3-codepipeline/4.3.8-s3-check.png)

6. Access the website using the URL in step 3 (S3 static website)

    ![S3 Website static hosting check](/images/4-cicd/4.3-codepipeline/4.3.9-web-check.png)

{{% notice note %}}
You can eliminate the S3 deployment step in codepipeline and use code to move the files in the public directory to the S3 bucket. However, I did not do this because I want to clearly distinguish between the build and deployment steps.
{{% /notice %}}