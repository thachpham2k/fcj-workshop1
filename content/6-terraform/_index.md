---
title: "Deploying with Terraform"
menuTitle: "Terraform"
date: "`r Sys.Date()`" 
weight: 6
chapter: false
pre: "<b> 6. </b>"
---

## Overview

**Terraform** is an open-source infrastructure as code tool that lets you build, change, and version cloud and on-prem resources safely and efficiently.

The basic structure of Terraform that we will use in this guide includes:
- **Blocks**: These are the fundamental components of Terraform configuration, with each block representing a part of the configuration such as resource, output, module, etc. In addition to the main blocks, we also use another type called nested blocks (blocks within blocks).
- **Arguments**: Used to configure attributes of blocks. One special type of argument can be used across multiple resource types but operates with a common mechanism called **meta-arguments** like `count`, `for_each`,...
- **Attributes**: Used to access values of resources after they are created.

**Modules**: To facilitate management, we divide the lab into 3 modules, categorized into 2 types:
- **Root Module**: The main module containing the entire Terraform configuration.
- **Child Module**: Submodules used within the root module to separate different parts of the configuration. In this section, we will build 2 child modules:
  - Static web module: Creating S3 and configuring a static website.
  - Cicd module: Serving the storage and deployment process of source code.

You can refer to the Terraform source code [here](https://github.com/thachpham2k/fcj-workshop1-terraform).

## Content

{{% children description="true" %}}

## Conclusion

After completing the lab, we hope you can draw conclusions about the two deployment methods.