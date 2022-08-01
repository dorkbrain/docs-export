---
title: "Adding a Trusted Root Certificate in CentOS"
date: "2017-08-26"
categories: 
  - "linux"
tags: 
  - "linux"
---

- Install the ca-certificates package: yum -y install ca-certificates
- Copy the certificate to /etc/pki/ca-trust/source/anchors
- Run: update-ca-trust extract
