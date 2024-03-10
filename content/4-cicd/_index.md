---
title : "Config Pipeline"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

In this phase, we will set up a Pipeline to automatically build and deploy the project. We will be using AWS services such as:

- CodeCommit to store source code
- AWS CodeBuild to perform the build process and create public files
- AWS CodePipeline to manage the entire process and deploy the files in the public directory to the pre-created S3 bucket.

## Content:

{{% children  %}}