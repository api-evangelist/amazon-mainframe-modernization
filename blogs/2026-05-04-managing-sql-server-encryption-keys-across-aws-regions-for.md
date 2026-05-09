---
title: "Managing SQL Server Encryption Keys Across AWS Regions for Disaster Recovery"
url: "https://aws.amazon.com/blogs/modernizing-with-aws/managing-sql-server-encryption-keys-across-aws-regions-for-disaster-recovery/"
date: "2026-05-04"
author: "Baris Furtinalar"
feed_url: "https://aws.amazon.com/blogs/modernizing-with-aws/feed/"
---
Introduction This blog post shows you how to back up, replicate, and restore the Microsoft SQL Server encryption key hierarchy across AWS Regions. This approach lets encrypted databases recover successfully during cross-region disaster recovery (DR) events on Amazon Elastic Compute Cloud (Amazon EC2). Cross-region DR planning for SQL Server on Amazon EC2 focuses on database replication and backup strategies. Encryption key management is critical. Many organizations overlook this aspect until a DR test fails. SQL Server uses a layered encryption hierarchy.
