---
title: "Creating Root Module"
menuTitle: "Module Root"
date: "`r Sys.Date()`" 
weight: 3
chapter: false
pre: "<b> 6.3. </b>"
description: "Declaration and usage of the modules created in previous steps."
---

## Overview

In this section, we will declare and use the modules created in previous steps. The purpose of using modules is to reuse code and create a structured organization and easier source code management.

## Implementation

Declaring Modules

In the root module file, we will declare the usage of two modules created earlier: `staticweb` and `cicd`. Below is how we declare these modules:

```hcl
module "s3" {
    source        = "./modules/staticweb"
    project-name  = var.project-name
    project-tags  = var.project-tags
}

module "codepipeline" {
    source        = "./modules/cicd"
    s3_bucket     = module.s3.s3-bucket
    project_name  = var.project-name
    repo_src_path = "src"
    project_tags  = var.project-tags
}
```

In the above code snippet:

- The `s3` module is used to create and configure an S3 static website. We provide the variables `project-name` and `project-tags` to this module.
- The `codepipeline` module is used to create and configure an AWS Pipeline to automatically build and deploy the website. We provide the variables `s3_bucket`, `project_name`, `repo_src_path`, and `project_tags` to this module.

This helps us organize the source code in a more structured manner and easily manage the components of the project.