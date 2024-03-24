---
title : "Deploy Hugo Website with AWS CI/CD Tools"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
---
# Deploying a Hugo Website with AWS CICD Tools

### Overall

In this blog post, we will explore how to deploy a Hugo website with Continuous Integration/Continuous Deployment (CI/CD). We will delve into some key concepts related to the deployment process of static S3 websites and three AWS CI/CD tools: AWS CodePipeline, AWS CodeCommit, and AWS CodeBuild. Not only applicable to deploying Hugo websites, you can also entirely apply this approach to deploy other front-end websites such as React, Vue, and many more.

In addition to deploying via the Console, we will also integrate Terraform to automate the construction of the lab. This will optimize the deployment process and infrastructure management efficiently.

![Diagram](/images/diagram.png)

### Content

{{% children  %}}
