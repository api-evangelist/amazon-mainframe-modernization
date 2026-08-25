---
title: "Zero-downtime multi-tenant IIS deployments on AWS Elastic Beanstalk"
url: "https://aws.amazon.com/blogs/modernizing-with-aws/zero-downtime-multi-tenant-iis-deployments-on-aws-elastic-beanstalk/"
date: "2026-08-18"
author: "Nag Shrenik Bandi"
feed_url: "https://aws.amazon.com/blogs/modernizing-with-aws/feed/"
---
Introduction If you host multiple ASP.NET web applications on a single AWS Elastic Beanstalk (Elastic Beanstalk) Windows Server environment, you face a common operational challenge: every deployment restarts Microsoft Internet Information Services (IIS), which takes down all sites on the instance, even the ones that did not change. For multi-tenant workloads where each IIS site […]
