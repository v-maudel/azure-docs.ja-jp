---
title: "DataDog を使用した Azure Kubernetes クラスターの監視 | Microsoft Docs"
description: "DataDog を使用した Azure Container Service での Kubernetes クラスターの監視"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: what-goes-here?
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
translationtype: Human Translation
ms.sourcegitcommit: 0aa9b3ae14f586fc79e6ebee898e794d526c19bd
ms.openlocfilehash: 2984a3811eba670b54fd253a8d7dc84acd8c8ec4


---

# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>DataDog を使用した Azure Container Service クラスターの監視

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、[Azure Container Service を使用して Kubernetes クラスターを作成](container-service-kubernetes-walkthrough.md)したことを想定します。

また、`az` Azure cli と `kubectl` ツールをインストールしていることも想定します。

`az` ツールがインストールされていることを確認するには、次を実行します。

```console
$ az --version
```

`az` ツールをインストールしていないない場合、[ここ](https://github.com/azure/azure-cli#installation)に手順が記載されています。

`kubectl` ツールがインストールされていることを確認するには、次を実行します。

```console
$ kubectl version
```

`kubectl` をインストールしていない場合、次を実行できます。

```console
$ az acs kubernetes install-cli
```

## <a name="datadog"></a>Datadog
Datadog は監視サービスです。Azure コンテナー サービス クラスター内のコンテナーから監視データを収集することができます。 Datadog には、コンテナー内の特定のメトリックを表示できる Docker 統合ダッシュボードがあります。 コンテナーから収集されたメトリックは、CPU、メモリ、ネットワーク、I/O 別に整理されます。 メトリックは Datadog によってコンテナーとイメージに分割されます。

最初に[アカウントを作成](https://www.datadoghq.com/lpg/)する必要があります

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>DaemonSet を使用した DataDog エージェントのインストール
DaemonSet は Kubernetes によって使用され、クラスターのホストごとに&1; つのコンテナーの&1; つのインスタンスを実行します。
この DaemonSet は、監視エージェントの実行に最適です。

DataDog にログインしたら、[DataDog の手順](https://app.datadoghq.com/account/settings#agent/kubernetes)に従って、DaemonSet を使用して DataDog エージェントをクラスターにインストールします。

## <a name="conclusion"></a>まとめ
これで完了です。 エージェントが実行されると、数分後にデータがコンソールに表示されます。 統合 [kubernetes ダッシュボード](https://app.datadoghq.com/screen/integration/kubernetes)にアクセスすると、クラスターの概要を確認できます。


<!--HONumber=Jan17_HO4-->


