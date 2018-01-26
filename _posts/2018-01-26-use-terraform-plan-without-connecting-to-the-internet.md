---
layout: post
title: "Use 'terraform plan' without connecting to the internet"
description: ""
category: "terraform"
tags: [terraform,aws,amazon]
---

You may be in the situation where you're trying to quickly test the output of a terraform plan, but you haven't actually got internet access, or valid AWS account credentials.

Simply paste this provider block, and terraform will be able to produce a new plan without connecting to the internet.

```bash
provider "aws" {
  skip_credentials_validation = true
  max_retries = 1
  skip_metadata_api_check = true
  access_key = "a"
  secret_key = "a"
  region = "us-west-2"

  endpoints {
    ec2 = "http://127.0.0.1:5000/"
  }
}
```

This only works on AWS AFAIK and I haven't found an equivalent for the AzureRM provider.