---
title: "Google Cloud Shell 创建项目并开启 API 服务生成 JSON 密钥"
date: 2022-05-06
slug: google-cloud-shell-project-json-key
description: "通过 Google Cloud Shell 创建新项目，开启指定 API 服务，创建服务账号并生成 JSON 密钥的完整命令行流程。"
categories:
  - 工作
tags:
  - Google Cloud
  - Cloud Shell
  - gcloud
  - API
  - JSON 密钥
summary: "通过 Google Cloud Shell 创建新项目，开启指定 API 服务，创建服务账号并生成 JSON 密钥的完整命令行流程。"
aliases:
  - /about-work/306.html
---

由于用到了Google一些api限制使用额度，用多个项目来补足配额问题：
代码很简单，说明文件里都有

## 1.添加新项目

`gcloud projects create 项目名`

## 2.切换到新项目

`gcloud config set project 项目名`

## 3.开通indexing api 服务

`gcloud services enable 服务名/api名`
如果不清除，可以使用：
`gcloud services list --available`
来查看可用的服务列表

## 4.创建服务账号

````shell
gcloud iam service-accounts create 服务账号 \
    --description="服务账号描述" \
    --display-name="服务账号名称"
````

## 5.为服务账号创建使用权限

````shell
gcloud projects add-iam-policy-binding 项目名 \
    --member="serviceAccount:服务账号@项目名.iam.gserviceaccount.com" \
    --role="roles/iam.serviceAccountUser"
````

## 6.生成json密钥

````shell
gcloud iam service-accounts keys create 文件名.json \
    --iam-account=服务账号@项目名.iam.gserviceaccount.com
````

## 7.打包所有文件

`tar czvf alljsonfile.tar.gz *`

## 8.下载到本地

`cloudshell download alljsonfile.tar.gz`

