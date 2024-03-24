---
title: "Managing Resources with Terraform"
menuTitle: "Terraform Manage"
date: "`r Sys.Date()`" 
weight: 4
chapter: false
pre: "<b> 6.4. </b>"
description: "Running Terraform commands to manage resources on the AWS platform, from provisioning to cleanup."
---

## Overview

In this section, we will execute Terraform commands to create and manage resources on AWS.

## Execution

First, open a terminal and navigate to the directory containing your Terraform configuration files.

```shell
cd path/to/terraform/files
```

Next, run the following commands:

```shell
terraform init
```

This command initializes Terraform and downloads necessary plugins for the providers declared in the configuration files.

```shell
terraform plan
```

This command examines the configuration and displays the expected changes to be applied when running `terraform apply`.

```shell
terraform apply -auto-approve
```

This command applies the changes to the AWS infrastructure automatically without requiring user confirmation.

## Check the Results

After running the `terraform apply` command successfully, verify on the AWS interface to ensure that the resources have been created and configured as expected.

Successful Apply command:

![Alt text](/fcj-workshop1/images/6-terraform/6.4-run/6.4.1-apply.png)

Check S3:

![Alt text](/fcj-workshop1/images/6-terraform/6.4-run/6.4.2-s3.png)

Check CodeCommit:

![Alt text](/fcj-workshop1/images/6-terraform/6.4-run/6.4.3-codecommit.png)

Check CodeBuild:

![Alt text](/fcj-workshop1/images/6-terraform/6.4-run/6.4.4-codebuild.png)

Check CodePipeline:

![Alt text](/fcj-workshop1/images/6-terraform/6.4-run/6.4.5-codepipeline.png)

Access the website via S3 Static Web URL:

![Alt text](/fcj-workshop1/images/6-terraform/6.4-run/6.4.6-website.png)

## Cleanup

Once you have completed your work and want to delete the created resources, you can run the following command:

```shell
terraform destroy -auto-approve
```

This command will automatically delete all resources created by Terraform without requiring user confirmation.