---

# required metadata
title: "Manual package install on R Server for Linux or Hadoop"
description: "Install individual packages on R Server on Linux or Hadoop"
keywords: ""
author: "HeidiSteen"
manager: "jhubbard"
ms.date: "04/10/2017"
ms.topic: "article"
ms.prod: "microsoft-r"
ms.service: ""
ms.assetid: ""

# optional metadata
ROBOTS: ""
audience: ""
ms.devlang: ""
ms.reviewer: ""
ms.suite: ""
ms.tgt_pltfrm: ""
ms.technology: "r-server"
ms.custom: ""
---

# Manual package installation (Microsoft R Server for Linux or Hadoop)

An alternative to running the install.sh script at the command line is to manually install each package and component.

## Syntax

With root privilege, you can use the `rpm -qi` command to install each package in the R Server distribution. The following example illustrates the syntax.

	`rpm -qi microsoft-r-open-3.3.3.x6486.rpm`

## Steps

1. Log in as root or a user with sudo privileges (`sudo su`). The following instructions assume user privileges with the sudo override.

2. Verify system repositories are up to date:
		[username] $ `sudo yum clean all`

3. Download and unpack the R Server distribution, following the instructions provided in [Offline installation](rserver-install-hadoop-offline.md).

4. Start with the .NET Core package from [http://www.microsoft.com/net/core](http://www.microsoft.com/net/core). This component is required for machine learning, remote execution, web service deployment, and configuration of web and compute nodes.

5. You should have the following packages, which should be installed in this order:

	microsoft-r-open-mro-3.3.3.x86_64.rpm
	microsoft-r-server-packages-9.1.rpm
	microsoft-r-server-hadoop-9.1.rpm
	microsoft-r-server-mml-9.1.rpm
	microsoft-r-server-mlm-9.1.rpm
	microsoft-r-server-config-rserve-9.1.rpm
	microsoft-r-server-computenode-9.1.rpm
	microsoft-r-server-webnode-9.1.rpm
	microsoft-r-server-adminutil-9.1.rpm

## Create folders and set permissions

A manual or custom installation must create the appropriate folders and set permissions.

**RPM or DEB Installers**

- `/var/RevoShare/` and `hdfs://user/RevoShare` must exist and have folders for each user running Microsoft R Server in Hadoop or Spark.
- `/var/RevoShare/<user>` and `hdfs://user/RevoShare/<user>` must have full permissions (all read, write, and executive permissions for all users).

**Cloudera Parcel Installers**

1. Create `/var/RevoShare/` and `hdfs://user/RevoShare`. Parcels cannot create them for you.
2. Give `/var/RevoShare/<user>` and `hdfs://user/RevoShare/<user>` for each user.
3. Grant full permissions to both `/var/RevoShare/<user>` and `hdfs://user/RevoShare/<user>`.

## Install additional packages on each node using rxExec

Once you have R Server installed on a node, you can use the `rxExec` function in [RevoScaleR](scaler/scaler.md) to install additional packages, including third-party packages from CRAN or another repository. 

For example, to install the `SuppDists` package on all the nodes of your cluster, call `rxExec` as follows:

	rxExec(install.packages, "SuppDists")
	
## Enable Remote Connections and Analytic Deployment

The server can be used as-is if you install and use an R IDE on the same box, but to benefit from the deployment and consumption of web services with Microsoft R Server, then you must configure R Server after installation to act as a deployment server and host analytic web services. Possible configurations are a [one-box setup](operationalize/configuration-initial.md) or an [enterprise setup](operationalize/configure-enterprise.md). Doing so also enables remote execution, allowing you to connect to R Server from an R Client workstation and execute code on the server.

## Next Steps

Review the following walkthroughs to move forward with using R Server and the RevoScaleR package in Spark and MapReduce processing models.

+ [Get started with ScaleR on Spark](scaler-spark-getting-started.md)
+ [Get started with ScaleR on MapReduce](scaler-hadoop-getting-started.md)