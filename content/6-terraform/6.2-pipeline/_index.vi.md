---
title : "Tạo Module cho Pipeline"
menuTitle : "Module CICD"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 6.2. </b> "
description: "Tạo một pipeline hoàn chỉnh sử dụng Terraform để tự động hóa quy trình xây dựng và triển khai một trang web tĩnh lên AWS."
---

### Tổng quan

Trong bước này, chúng ta sẽ tạo một pipeline hoàn chỉnh sử dụng Terraform để tự động hóa quy trình xây dựng và triển khai một trang web tĩnh lên AWS.

Đây là [Bước 4: THIẾT LẬP CICD](https://thachpham2k.github.io/fcj-workshop1/vi/4-cicd/) 

### Chuẩn bị

1. File `buildspec.yml`
    
    File `buildspec.yml` này tương tự như mục *1. Tạo tệp buildspec.yml* phần [Thực hành của TẠO CODEBUILD](https://thachpham2k.github.io/fcj-workshop1/vi/4-cicd/4.2-codebuild/#th%E1%BB%B1c-h%C3%A0nh)

    Tuy nhiên, ở phần trước mình đã giới thiệu về việc bỏ đi bước triển khai và sử dụng CodeBuild để đẩy file lên S3 bucket luôn. Để làm điều đó, mình thêm một phase là `post_build` đi kèm với lệnh của AWS CLI (`aws s3 cp`) để gửi file (đoạn này đã được comments). Đây chỉ là một giới thiệu về cách thức hoạt động, không phải là cách chúng ta sẽ thực hiện thực tế 😁🤣.

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

2. IAM Role cho CodePipeline

    Đây là [Phần 2.3 TẠO IAM CHO CODEBUILD VÀ CODEPIPELINE](https://thachpham2k.github.io/fcj-workshop1/vi/2-prerequiste/2.3-iam-role/)
    
    Tuy nhiên, thay vì role này được cấu hình resource là `*`, chúng ta giới hạn quyền cho chúng chỉ xoay quanh những resource được sử dụng (Ở phần trước các resource như S3 Bucket, CodeCommit,.. được tạo sau nên chúng ta không thể chỉ định đúng tên của chúng để tránh trường hợp ARN được xác định trước giống với lúc triển khai gây lỗi). 

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

    Ở phần này, chúng ta không sử dụng file `*.tpl` kết hợp với function `templatefile` để khai báo policy nữa mà sử dụng here-doc (một cách để khai báo đoạn mã).

    Tham khảo bài [Here Document and Here String](https://www.baeldung.com/linux/heredoc-herestring) (nếu bạn muốn tìm hiểu thêm về nội dung này) (Tuy nhiên phần *Tab Suppression* ko hoạt động như tác giả nói nhưng các phần sau thì Oke và tác giả giải thích rất dễ hiểu nên mình vẫn gợi ý bài viết này).

3. S3 Artifact

    Trong phần này chúng ta sẽ tạo một S3 Bucket để CodePipeline lưu artifact trong quá trình thực hiện (Bước này không có trong phần trước vì AWS CodePipeline tự tạo).

    Trong phần này, thay vì sử dụng resource `random_integer` như trước đó, thì mình dùng `random_uuid` đi kèm với `substr` (sub string) để giới hạn số kí tự là 5 (`substr(random_uuid.s3-uuid.result, 0, 5)`).

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

    Ở đây, chúng ta tạo một bucket S3 để lưu trữ artifact. Sử dụng `random_uuid` để tạo một UUID ngẫu nhiên, và sau đó sử dụng `substr` để giới hạn độ dài của UUID là 5 ký tự. Điều này giúp đảm bảo tên bucket là duy nhất và dễ đọc. 
    
    Bucket này cũng được cấu hình với `force_destroy = true` để đảm bảo rằng nó có thể bị xóa mà không cần xác nhận thêm.
    
    Cuối cùng, chúng ta đảm bảo rằng bucket có ACL private và tuân thủ nguyên tắc sở hữu bằng cách tạo các tài nguyên `aws_s3_bucket_ownership_controls` và `aws_s3_bucket_acl`.

4. Upload file

    Mục đích của bước này là đẩy mã nguồn của trang web lên AWS CodeCommit. Terraform cung cấp những resource liên quan đến lệnh git (tham khảo ở [git Provider](https://registry.terraform.io/providers/metio/git/latest/docs)) nhưng chúng ta lại sử dụng một cách rất sơ khai là sử dụng `provisioner` đi kèm với lệnh Linux cơ bản.

    Ở đây, mình sử dụng một resource là `terraform_data`. Resource này được sử dụng để sử dụng các tính năng của resource mà không cần tạo resource thực sự. Có một resource khác cũng có chức năng tương tự là [`null_resource`](https://registry.terraform.io/providers/hashicorp/null/latest/docs/resources/resource), nhưng từ version 1.4 terraform khuyến nghị nên sử dụng `terraform_data`.

    ```hcl
    # Tạo một commit và tải lên tệp từ thư mục cục bộ
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

    Ở đây, chúng ta sử dụng `local-exec` provisioner để thực thi các lệnh cục bộ. Đầu tiên, một tệp script `CredentialHelper.sh` được thực thi để đảm bảo xác thực với AWS. Sau đó, chúng ta thực hiện các lệnh git để tạo một commit mới và đẩy mã nguồn lên CodeCommit.

### Thực hiện

1. CodeCommit

    Ở bước này, chúng ta sẽ tạo AWS CodeCommit repository. Tương tự như mục 2 phần [Thực hành của TẠO CODECOMMIT ĐỂ LƯU TRỮ MÃ NGUỒN](https://thachpham2k.github.io/fcj-workshop1/vi/4-cicd/4.1-codecommit/#th%E1%BB%B1c-h%C3%A0nh)

    ![Repository settings](/$REPO_NAMEimages/4-cicd/4.1-codecommit/4.1.1-create.png)

    ```hcl
    resource "aws_codecommit_repository" "codecommit" {
        repository_name = "${var.project_name}-codecommit"
        tags = var.project_tags
    }
    ```

2. CodeBuild

    Bước này tương tự như mục 3 phần [Thực hành của TẠO CODEBUILD](https://thachpham2k.github.io/fcj-workshop1/vi/4-cicd/4.2-codebuild/#th%E1%BB%B1c-h%C3%A0nh)

    ![Source](/$REPO_NAMEimages/4-cicd/4.2-codebuild/4.2.3-create.png)

    Ở phần `source` (CodeCommit), nếu cấu hình reference type là branch `main`, chúng ta sử dụng argument `source_version = main` hoặc `source_version = refs/heads/main`.

    Trong nested block `environment`, chúng ta tạo một nested block nữa là `environment_variable`. Phần này dùng để truyền biến môi trường cho build script (phần này mình tạo để nếu bạn muốn ko sử dụng bước deploy mà thay vào đó là push file trực tiếp sang s3 bằng lệnh như đã nói).

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
    
    Bước này tương tự như mục 3 phần [Thực hành của TẠO CODEPIPELINE](https://thachpham2k.github.io/fcj-workshop1/vi/4-cicd/4.3-codepipeline/#th%E1%BB%B1c-h%C3%A0nh)

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

    Ở đây, chúng ta định nghĩa một pipeline CodePipeline. Chúng ta chỉ cần cung cấp `name` và `role_arn`. `artifact_store` được cấu hình để lưu trữ các artifact tạo ra trong quá trình thực thi pipeline.

    Mỗi `stage` trong pipeline đại diện cho một phần của quy trình triển khai. Mỗi `action` trong stage thực hiện một tác vụ cụ thể. Ví dụ, stage "Source" trích xuất mã nguồn từ CodeCommit, stage "Build" sử dụng CodeBuild để xây dựng mã nguồn, và stage "Deploy" triển khai mã nguồn lên S3.

    Mỗi action cũng có các `configuration` tương ứng để chỉ định cách thức thực hiện nhiệm vụ cụ thể. Ví dụ, trong action "Source", chúng ta cấu hình repository và branch để trích xuất mã nguồn.
