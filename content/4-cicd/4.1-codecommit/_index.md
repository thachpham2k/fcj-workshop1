---
title : "Create CodeCommit"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 4.1. </b> "
---
## Overview

AWS CodeCommit is a fully managed source control service that makes it easy for teams to collaborate securely on source code in private Git repositories.
- CodeCommit allows developers to store source code in private Git repositories and collaborate on source code changes with colleagues and contributions from external parties.
- It integrates with other AWS services like CodePipeline to trigger CI/CD workflows directly from a CodeCommit repository.
- Common Git commands and tools can be used to work with CodeCommit repositories, eliminating the need to manage your own source control infrastructure.

## Practice

1. Access the **AWS CodeCommit** service or through the link [AWS CodeCommit](https://console.aws.amazon.com/codesuite/codecommit/repositories)

2. Choose **Create Repository**

3. In the **Create Repository** interface:
   
    - In the **Repository name** field, enter `workhsop-codecommit`
    ![Repository settings](/fcj-workshop1/images/4-cicd/4.1-codecommit/4.1.1-create.png)

    - Scroll down to the bottom of the page and select **Create**

4. In the **CodeCommit Repositories** interface, select the repository you just created (`workshop-codecommit`). Under **Connection Steps**, save the repository URL.

    ![Connection Steps](/fcj-workshop1/images/4-cicd/4.1-codecommit/4.1.2-connection.png)

5. Log in to AWS CLI and git

    > 👉 To push source code to CodeCommit, you first need to add CodeCommit authentication information to Git using AWS Credentials.
    
    1. Add AWS Credentials to the Terminal: Use the aws token and the `aws configure` command to authenticate AWS CLI. If you are not familiar with this, you can refer to the tutorial [Authenticate with IAM user credentials](https://docs.aws.amazon.com/cli/latest/userguide/cli-authentication-user.html).
   2. Log in to Git and configure it: Next, you need to log in to Git and configure it by following these steps:

    ```shell
    # Login to aws cli
    aws configure

    # Configure Git to use aws codecommit credential-helper
    git config --global credential.helper '!aws codecommit credential-helper $@'
    # Enable using HTTP path for Git
    git config --global credential.UseHttpPath true
    ```
    ![Config aws cli and git](/fcj-workshop1/images/4-cicd/4.1-codecommit/4.1.3-cli-config.png)

6. Hugo project source code {{% button href="/source/000000-Workshop.zip" icon="fas fa-download" icon-position="right" %}}Download Source{{% /button %}}

7. Finally, push the source code to CodeCommit by following these steps:

    ```shell
    git init
    git remote add origin https://git-codecommit.ap-southeast-1.amazonaws.com/v1/repos/workshop-codecommit
    git add .
    git commit -m "first commit"
    git push --set-upstream origin master
    ```

    Note: https://git-codecommit.ap-southeast-1.amazonaws.com/v1/repos/workshop-codecommit is the path to the codecommit repo URL obtained in step 4 (In the Connection Steps section).
  
    ![Push code to AWS CodeCommit](/fcj-workshop1/images/4-cicd/4.1-codecommit/4.1.4-git-commit.png)
  
    ![Push code to AWS CodeCommit (2)](/fcj-workshop1/images/4-cicd/4.1-codecommit/4.1.5-git-commit.png)

8. Once completed, your source code has been successfully pushed to AWS CodeCommit.

![Push sourcecode success](/fcj-workshop1/images/4-cicd/4.1-codecommit/4.1.6-commit-success.png)

## Attached Documents

{{%attachments style="blue" /%}}