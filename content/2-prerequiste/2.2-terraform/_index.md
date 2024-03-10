---
title : "Install terraform"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2. </b> "
---

## Overview

{{% notice info %}}
Terraform is an open-source infrastructure as code (IaC) tool developed by HashiCorp. Users declare and provide data center infrastructure using a declarative configuration language called HashiCorp Configuration Language, or can opt for JSON.
{{% /notice %}}

> 👉 **Brief**    
Terraform is an IaC (infrastructure as code) tool 

## Content

To install Terraform, you can use the following commands:

```shell
# Install ultilities tools
sudo apt-get update -y
sudo apt-get install -y gnupg software-properties-common
# Update Hashicorp GDG key
wget -O- https://apt.releases.hashicorp.com/gpg | \
    gpg --dearmor | \
    sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
# Verify key
gpg --no-default-keyring \
    --keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
    --fingerprint
# Add HashiCorp repository to apt repository
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
    https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
    sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update
# Install Terraform
sudo apt-get install terraform
# Check the version of Terraform
terraform -version
```

This is how you install Terraform on Ubuntu. If you're installing on a different platform or want to learn more about the installation process, you can refer to the documentation [here](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)