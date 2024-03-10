---
title : "Install S3 Static Web"
date :  "`r Sys.Date()`" 
weight : 3 
chapter : false
pre : " <b> 3. </b> "
---

## Overview

The first step is still to create a website first, the content will come later 😂. To host a static website, AWS provides Static website hosting feature based on Amazon S3.

<details>
<summary>
<b>Amazon S3 (Simple Storage Service)</b>: is an object storage service built to store and retrieve any amount of data from anywhere.
</summary>
    
Some key things to know about S3:
- S3 provides extremely durable storage for all types of data including text, images, videos, etc. Data is stored across multiple devices and facilities and is designed to sustain the loss of 2 facilities concurrently.
- Objects are stored and retrieved via a simple REST API. Common ways to interface with S3 include the AWS CLI, SDKs for various languages like Java, Python etc. and the S3 console.
- Buckets are used to organize objects in S3. You can create multiple public or private buckets to categorize your data.
- Access control is granular down to the individual object level. You can set permissions for who can access, read or write each object using IAM policies and roles.
- Common use cases for S3 include serving static websites, storing application data and backups, archiving data long term and exchanging data between applications.
</details>

## Thực hành

1. Access the AWS S3 service on the console or through the link [AWS S3](https://s3.console.aws.amazon.com/s3/home)

1. Choose **Create Bucket**.

1. On the **Create bucket**
    
    - Region: `ap-southeast-1`
    - Bucket name: `workshop1-123`
    
    > 💭 **Mẹo**   
    Since the bucket name must be unique, I usually add a sequence of numbers after the desired name.
    
    ![General configuration](/fcj-workshop1/images/3-s3_staticweb/3.1-create.png)

    - Object Ownership: `ACLs enabled`
    
    ![Object ownership](/fcj-workshop1/images/3-s3_staticweb/3.2-create.png)

    - Turn off **Block all public access**
    - Click `I acknowledge that the current settings might result in this bucket and the objects within becoming public.` in the warning **Turning off block all public access might result in this bucket and the objects within becoming public**
    
    ![Block public access settings for this bucket](/fcj-workshop1/images/3-s3_staticweb/3.3-create.png)

    - Choose `Create Bucket`

   -> Bucket `workshop1-123` create success.
   
1. Feature **S3 static hosting** in the **Properties** tab. Choose **Properties** and then navigate to **Static website hosting** at the bottom of the tab.

    ![Select Properties tab](/fcj-workshop1/images/3-s3_staticweb/3.5-config.png)

1. Choose **Edit** in **Static website hosting**
 
    ![Edit Static Website Hosting feature](/fcj-workshop1/images/3-s3_staticweb/3.6-config.png)

1. On the **Edit static website hosting**:

   1. **Static website hosting**: `enable`
   1. **Index document**: `index.html`
   
        ![Static Website Hosting interface](/fcj-workshop1/images/3-s3_staticweb/3.7-config.png)

    1. Click **Save changes**

1. At the **Static website hosting** feature, save the **bucket website endpoint** url for later use

    ![Enable Static Website Hosting success](/fcj-workshop1/images/3-s3_staticweb/3.8-config.png)

    > ℹ️  **Note**     
    > At this point, this S3 website endpoint may still not be working because:
    > - The website does not have the **index.html** file to display.
    > - Even if there is a file, users still do not have permission to access files within S3.
    > -> The next step will configure a policy to grant users access to the content of files inside S3. 

    ![Access S3 static website using http](/fcj-workshop1/images/3-s3_staticweb/3.9-check.png)

1. Choose **Permissions** tab
1. Navigate to the **Bucket policy** section, choose **Edit**

    ![alt text](/fcj-workshop1/images/3-s3_staticweb/3.10-policy.png)

1. Paste the following json policy snippet:.

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Sid": "Statement1",
                "Principal": "*",
                "Effect": "Allow",
                "Action": [
                    "s3:GetObject"
                ],
                "Resource": [
                    "arn:aws:s3:::workshop1-123/*"
                ]
            }
        ]
    }
    ```

    > 👉 **Brief**    
    This JSON policy configures permissions to allow all users (Principal) to perform the action (Action) `s3:GetObject` on objects within the bucket named `workshop1-123`. This means that everyone, including unidentified users, has the permission to access and read (GetObject) objects within this bucket.

4. Choose **Save changes**

-> The creation and configuration of a website using the **Static website hosting** feature of S3 is now complete.