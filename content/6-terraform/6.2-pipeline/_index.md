---
title: "Creating Pipeline Module"
menuTitle: "Module CICD"
date: "`r Sys.Date()`" 
weight: 2
chapter: false
pre: "<b> 6.2. </b>"
description: "Creating a complete pipeline using Terraform to automate the process of building and deploying a static website onto AWS."
---

### Overview

In this step, we will create a complete pipeline using Terraform to automate the process of building and deploying a static website onto AWS.

This is [Step 4: SETTING UP CI/CD](https://thachpham2k.github.io/fcj-workshop1/4-cicd/)

### Preparation

1. `buildspec.yml` File

    This `buildspec.yml` file is similar to the *1. Create buildspec.yml file* section in the [CODEBUILD PRACTICE](https://thachpham2k.github.io/fcj-workshop1/en/4-cicd/4.2-codebuild/#practice). However, in the previous section, we introduced skipping the deployment step and using CodeBuild to push files directly to the S3 bucket. To achieve this, we add a `post_build` phase along with the AWS CLI command (`aws s3 cp`) to send the files (this section has been commented out). This is just an introduction to how it works, not how we will actually do it 😁🤣.

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
        # post_build:
        #     commands:
        #     - aws s3 cp public s3://$S3_BUCKET --recursive --exclude 'index.html'
        #     - aws s3 cp public/index.html s3://$S3_BUCKET
    artifacts:
    files:
        - '**/*'
    base-directory: 'public'
    ```

2. IAM Role for CodePipeline

    This is [Section 2.3 CREATE IAM FOR CODEBUILD AND CODEPIPELINE](https://thachpham2k.github.io/fcj-workshop1/en/2-prerequiste/2.3-iam-role/)
    
    However, instead of configuring this role with `*` resources, we limit its permissions to focus only on the resources used (In the previous section, resources like S3 Bucket, CodeCommit, etc., are created later, so we cannot specify their names to avoid predefined ARNs causing errors). 

    ```hcl
    resource "aws_iam_role" "codepipeline_role" {
        name = "${var.project_name}-pipeline-role"

        assume_role_policy = <<EOF
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
        EOF
    }

    resource "aws_iam_role_policy" "codepipeline_policy" {
        name = "codepipeline-policy"
        role = aws_iam_role.codepipeline_role.id

        policy = <<EOF
        {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Effect": "Allow",
                    "Action": [
                        "s3:*"
                    ],
                    "Resource": [
                        "${var.s3_bucket.arn}",
                        "${var.s3_bucket.arn}/*",
                        "${aws_s3_bucket.s3_artifacts.arn}",
                        "${aws_s3_bucket.s3_artifacts.arn}/*"
                    ]
                },
                {
                    "Effect": "Allow",
                    "Resource": [
                        "*"
                    ],
                    "Action": [
                        "logs:CreateLogGroup",
                        "logs:CreateLogStream",
                        "logs:PutLogEvents"
                    ]
                },
                {
                    "Effect": "Allow",
                    "Resource": [
                        "${aws_codecommit_repository.codecommit.arn}"
                    ],
                    "Action": [
                        "codecommit:*"
                    ]
                },
                {
                    "Effect": "Allow",
                    "Action": [
                        "codebuild:BatchGetBuilds",
                        "codebuild:StartBuild"
                    ],
                    "Resource": "*"
                }
            ]
        } 
        EOF
    }
    ```

    In this section, we don't use the `*.tpl` file combined with the `templatefile` function to declare the policy anymore. Instead, we use here-doc (a way to declare code blocks). You can refer to the article [Here Document and Here String](https://www.baeldung.com/linux/heredoc-herestring) if you want to learn more about this content.

3. S3 Artifact

    In this section, we will create an S3 Bucket for CodePipeline to store artifacts during the execution process (This step was not in the previous section because AWS CodePipeline automatically creates it).

    In this section    instead of using the `random_integer` resource as before, we use `random_uuid` along with `substr` (substring) to limit the number of characters to 5 (`substr(random_uuid.s3-uuid.result, 0, 5)`).

    ```hcl
    resource "random_uuid" "s3-uuid" {
    }

    resource "aws_s3_bucket" "s3_artifacts" {
        bucket = "${var.project_name}-artifact-${substr(random_uuid.s3-uuid.result, 0, 5)}"
        force_destroy = true
        tags = var.project_tags
    }

    resource "aws_s3_bucket_ownership_controls" "s3_artifacts_owner" {
        bucket = aws_s3_bucket.s3_artifacts.id
        rule {
            object_ownership = "BucketOwnerPreferred"
        }
    }

    resource "aws_s3_bucket_acl" "s3_artifacts_acl" {
        bucket = aws_s3_bucket.s3_artifacts.id
        acl    = "private"
        depends_on = [ aws_s3_bucket_ownership_controls.s3_artifacts_owner ]
    }
    ```

    Here, we create an S3 bucket to store artifacts. We use `random_uuid` to generate a random UUID, and then use `substr` to limit the length of the UUID to 5 characters. This ensures that the bucket name is unique and readable. 
    
    This bucket is also configured with `force_destroy = true` to ensure that it can be destroyed without additional confirmation.
    
    Finally, we ensure that the bucket has a private ACL and obeys the ownership principle by creating the `aws_s3_bucket_ownership_controls` and `aws_s3_bucket_acl` resources.

4. Upload File

    The purpose of this step is to push the source code of the website to AWS CodeCommit. Terraform provides resources related to git commands (refer to [git Provider](https://registry.terraform.io/providers/metio/git/latest/docs)), but we use a very rudimentary way by using `provisioner` along with basic Linux commands.

    Here, I use a resource called `terraform_data`. This resource is used to utilize the features of a resource without actually creating the resource. There is another resource with a similar function called [`null_resource`](https://registry.terraform.io/providers/hashicorp/null/latest/docs/resources/resource), but from version 1.4, Terraform recommends using `terraform_data`.

    ```hcl
    # Create a commit and upload files from the local directory
    resource "terraform_data" "upload_file" {
        depends_on = [
            aws_codecommit_repository.codecommit,
        ]
        provisioner "local-exec" {
            command = "bash ${path.root}/${path.module}/CredentialHelper.sh"
        }
        provisioner "local-exec" {
            command = <<-EOT
            cd ${path.root}/${var.repo_src_path} && \
            rm -rf .git && \
            git init && \
            git add . && \
            git commit -m 'automation commit' && \
            git remote add origin ${aws_codecommit_repository.codecommit.clone_url_http} && \
            git push origin HEAD:main
            EOT
        }
    }
    ```

    Here, we use the `local-exec` provisioner to execute local commands. First, a script file `CredentialHelper.sh` is executed to ensure authentication with AWS. Then, we execute git commands to create a new commit and push the source code to CodeCommit.

### Implementation

1. CodeCommit

    In this step, we will create an AWS CodeCommit repository. Similar to Section 2 in [CODECOMMIT SETUP FOR SOURCE CODE STORAGE](https://thachpham2k.github.io/fcj-workshop1/4-cicd/4.1-codecommit/#practice).

    ![Repository settings](/fcj-workshop1/images/4-cicd/4.1-codecommit/4.1.1-create.png)

    ```hcl
    resource "aws_codecommit_repository" "codecommit" {
        repository_name = "${var.project_name}-codecommit"
        tags = var.project_tags
    }
    ```

2. CodeBuild

    This step is similar to Section 3 in [CREATE CODEBUILD](https://thachpham2k.github.io/fcj-workshop1/4-cicd/4.2-codebuild/#practice).

    ![Source](/fcj-workshop1/images/4-cicd/4.2-codebuild/4.2.3-create.png)

    In the `source` section (CodeCommit), if the reference type is the `main` branch, we use the argument `source_version = main` or `source_version = refs/heads/main`.

    In the nested `environment` block, we create another nested block called `environment_variable`. This part is used to pass environment variables to the build script (this part is created in case you don't want to use the deploy step and instead directly push files to S3 using commands as mentioned).

    ```hcl
    resource "aws_codebuild_project" "codebuild" {
        name          = "${var.project_name}-codebuild"
        service_role  = aws_iam_role.codepipeline_role.arn

        source_version = "main"

        artifacts {
            type = "NO_ARTIFACTS"
        }

        source {
            type            = "CODECOMMIT"
            location        = aws_codecommit_repository.codecommit.clone_url_http
            git_clone_depth = 1
           ```hcl
            buildspec = "buildspec.yml"
        }

        environment {
            compute_type = "BUILD_GENERAL1_SMALL"
            image        = "aws/codebuild/standard:7.0"
            type         = "LINUX_CONTAINER"
            environment_variable {
                name  = "S3_BUCKET"
                value = var.s3_bucket.bucket
            }
        }

        tags = var.project_tags
    }   
    ```

3. CodePipeline
    
    This step is similar to Section 3 in [CREATE CODEPIPELINE](https://thachpham2k.github.io/fcj-workshop1/en/4-cicd/4.3-codepipeline/#practice).

    ```hcl
    resource "aws_codepipeline" "codepipeline" {
        name = "${var.project_name}-codepipeline"

        role_arn = aws_iam_role.codepipeline_role.arn

        artifact_store {
            location = aws_s3_bucket.s3_artifacts.bucket
            type     = "S3"
        }

        stage {
            name = "Source"

            action {
                name             = "Source"
                category         = "Source"
                owner            = "AWS"
                provider         = "CodeCommit"
                version          = "1"
                output_artifacts = ["source_output"]

                configuration = {
                    RepositoryName = aws_codecommit_repository.codecommit.id
                    BranchName     = "main"
                }
            }
        }

        stage {
            name = "Build"

            action {
                name             = "Build"
                category         = "Build"
                owner            = "AWS"
                provider         = "CodeBuild"
                input_artifacts  = ["source_output"]
                output_artifacts = ["build_output"]
                version          = "1"

                configuration = {
                    ProjectName = aws_codebuild_project.codebuild.name
                }
            }
        }

        stage {
            name = "Deploy"

            action {
                name            = "Deploy"
                category        = "Deploy"
                owner           = "AWS"
                provider        = "S3"
                input_artifacts = ["build_output"]
                version         = "1"

                configuration = {
                    BucketName = var.s3_bucket.bucket
                    Extract    = "true"
                }
            }
        }

        tags = var.project_tags
    }
    ```

    Here, we define a CodePipeline pipeline. We only need to provide `name` and `role_arn`. `artifact_store` is configured to store artifacts generated during pipeline execution.

    Each `stage` in the pipeline represents a part of the deployment process. Each `action` in the stage performs a specific task. For example, the "Source" stage extracts source code from CodeCommit, the "Build" stage uses CodeBuild to build the source code, and the "Deploy" stage deploys the source code to S3.

    Each action also has corresponding `configuration` to specify how the specific task should be performed. For example, in the "Source" action, we configure the repository and branch to extract source code.