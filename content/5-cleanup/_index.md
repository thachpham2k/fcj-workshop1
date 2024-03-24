---
title : "Clean up resources"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : "<b>5. </b>"
---

We will take the following steps to delete the resources we created in this exercise.

## Delete CodePipeline

1. Access the [AWS CodePipeline service](https://console.aws.amazon.com/codesuite/codepipeline/pipelines)

2. Select the Pipeline you created (e.g., `workshop-codepipeline`)

    - Choose **Delete pipeline**
  
    ![Select pipeline](/$REPO_NAMEimages/5-cleanup/5.1-codepipeline.png)

    - Enter `delete` and then choose **Delete**

    ![Delete pipeline](/$REPO_NAMEimages/5-cleanup/5.2-codepipeline.png)

## Delete CodeBuild

1. Access the [AWS CodeBuild service](https://console.aws.amazon.com/codesuite/codebuild/projects)

2. Select the build project you created (e.g., `workshop-codebuild`)

    - Choose **Action** and then select **Delete**

    ![Select](/$REPO_NAMEimages/5-cleanup/5.3-codebuild.png)

    - Enter `delete` and then choose **Delete**

    ![Delete](/$REPO_NAMEimages/5-cleanup/5.4-codebuild.png)

## Delete CodeCommit

1. Access the [AWS CodeCommit service](https://console.aws.amazon.com/codesuite/codecommit/repositories)

2. Select the repository you created (e.g., `workshop-codecommit`)

    - Choose **Delete repository**

    ![Select repository](/$REPO_NAMEimages/5-cleanup/5.5-codecommit.png)

    - Enter `delete` and then choose **Delete**

## Delete S3

1. Access the [S3 management console](https://s3.console.aws.amazon.com/s3/home)
    - Click on the S3 bucket you created for this lab (e.g., `workshop1-123`)
    - Click **Empty**.
    
    ![Select bucket](/$REPO_NAMEimages/5-cleanup/5.6-s3-website.png)

    - Enter **permanently delete**, then click **Empty** to delete all objects in the bucket.
    
    ![Empty bucket](/$REPO_NAMEimages/5-cleanup/5.7-s3-website.png)

    - Click **Exit**.

2. After emptying the bucket, click **Delete**

    ![Select bucket](/$REPO_NAMEimages/5-cleanup/5.8-s3-website.png)

3. Enter the S3 bucket name (`workshop1-123`), then click **Delete bucket** to delete the S3 bucket.

    ![Delete bucket](/$REPO_NAMEimages/5-cleanup/5.9-s3-website.png)

4. Apart from the S3 bucket we created, there is another bucket used by **AWS CodePipeline** to store artifacts during pipeline execution with a name like `codepipeline-<region>-*` (e.g., `codepipeline-ap-southeast-1-661747411738`)

5. Access the bucket by selecting its name.

    - Find the Object `workshop-codepipline/` (the name of the pipeline created in the AWS CodePipeline section)

    ![Bucket](/$REPO_NAMEimages/5-cleanup/5.10-s3-pipeline.png)

    - Enter **permanently delete**, then click **Delete Objects** to delete objects in the bucket.

    ![Delete Object](/$REPO_NAMEimages/5-cleanup/5.11-s3-pipeline.png)

## Delete IAM Role

1. Access the [IAM management console](https://console.aws.amazon.com/iamv2/home#/home)

    - Click **Roles**.
    - In the search box, enter `fcj-workshop-role`.
    - Select **fcj-workshop-role**.
    - Click **Delete**, then enter the role name `fcj-workshop-role` and click **Delete** to delete the role.

    ![IAM Roles](/$REPO_NAMEimages/5-cleanup/5.12-role.png)

## Delete IAM Policy

1. Access the [IAM management console](https://console.aws.amazon.com/iamv2/home#/home)

    - Click **Policies**.
    - In the search box, enter `fcj-workshop`.
    - Select **fcj-workshop**.
    - Click **Delete**, then enter the policy name `fcj-workshop` and click **Delete** to delete the policy.

    ![IAM Policies](/$REPO_NAMEimages/5-cleanup/5.13-policy.png)