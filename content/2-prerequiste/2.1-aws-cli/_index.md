---
title : "Install AWS CLI"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.1. </b> "
---

## Overview

{{% notice info %}}
AWS Command Line Interface (CLI) is a tool to manage your AWS services. With just one tool to download and configure, you can control multiple AWS services through a command line and automate them with command sets.
{{% /notice %}}

> 👉 **Brief**    
AWS Command Line Interface (CLI) is a tool that allows you to interact with AWS services via the command line on the terminal.

## Content

To install AWS CLI version 2, use the following commands:

```shell
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
# Remove install package
rm -rf awscliv2.zip aws/install
```

This is how you install AWS CLI on Linux. If you're installing on a different platform or want to learn more about installing or updating from AWS CLI to AWS CLI version 2, you can refer to the documentation [here](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)