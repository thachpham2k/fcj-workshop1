---
title : "Create CodeBuild"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 4.2. </b> "
---

## Overview

{{% notice note %}}
**AWS CodeBuild** is a fully managed continuous integration service that compiles source code, runs tests, and produces ready-to-deploy software packages.
{{% /notice %}}

Some key features of AWS CodeBuild include:
- It allows you to use pre-packaged build environments provided by AWS such as Ubuntu, Windows, etc., or use a custom build environment with your own build tools.
- CodeBuild automatically scales up and down to handle multiple simultaneous build processes, allowing the build process to proceed without waiting in a queue.
- You only pay for the compute resources used in your build process, so there are no upfront costs or need to provision servers.
- To get started with CodeBuild, you create a build project that specifies the source location, build environment to use, and build commands.
- Once a project is created, you can manually trigger builds or set up continuous integration by integrating with code repositories like GitHub.
- Build logs and build artifacts are stored so you can debug issues or deploy packages after a successful build.


## Practice

1. Create a `buildspec.yml` file in the source code

    The Buildspec file is a configuration file used to specify the steps to be performed in the build process when using the AWS CodeBuild service.

    Contents of the `buildspec.yml` file:

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
    artifacts:
      files:
        - '**/*'
      base-directory: 'public'
    ```

2. Push the new source code containing the `buildspec.yml` file to CodeCommit

    After creating the `buildspec.yml` file, use the following commands to push the source code to CodeCommit

    ```shell
    git add .
    git commit -m "add buildspec.yml file"
    git push
    ```

    ![Push buildspec.yml file to Codecommit](/fcj-workshop1/images/4-cicd/4.2-codebuild/4.2.1-add-buildspec.png)

3. Access the AWS CodeBuild service or through the link [AWS CodeBuild](https://console.aws.amazon.com/codesuite/codebuild/projects)

4. Choose **Create project**

5. In the **Create project** interface:

     - **Project configuration** section: In the **project name** field, enter `workshop-codebuild`
     
       ![Project configuration](/fcj-workshop1/images/4-cicd/4.2-codebuild/4.2.2-create.png)

     - **Source** section:
       - For **Source provider**, choose **AWS CodeCommit**
       - For **repository**, enter `workshop-codecommit`
       - For **reference type**, choose **Branch**
       - For **Branch**, select **master**
       
       ![Source](/fcj-workshop1/images/4-cicd/4.2-codebuild/4.2.3-create.png)

     - **Environment** section:
       - For **Compute**, choose **EC2**
       - For **Operating system**, choose **Ubuntu**
       - For **Runtime(s)**, choose **Standard**
       - For **Image**, select **aws/codebuild/standard:7.0**
       - For **Service role**, choose **Existing service role**
       - For **Role ARN**, select the ARN of the role created in step 2.3
       
       ![Environment](/fcj-workshop1/images/4-cicd/4.2-codebuild/4.2.4-create.png)
     
     - **Buildspec** section:
       - For **Build specifications**, choose **Use a buildspec file**
       - For **Buildspec name**, enter `buildspec.yml` (enter the location where the buildspec file is saved in step 1)

       ![Buildspec](/fcj-workshop1/images/4-cicd/4.2-codebuild/4.2.5-create.png)

6. Click **Create Build project** at the bottom of the page

7. Once the build project is created, you can test it by clicking the orange **Start build** button on the right. The result of the build process will be displayed on the **Build history** tab.

  ![Test build](/fcj-workshop1/images/4-cicd/4.2-codebuild/4.2.6-build.png)
