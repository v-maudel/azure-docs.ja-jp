---
title: "Azure IoT への Intel Edison (C) の接続 - レッスン 3: Function App の作成 | Microsoft Docs"
description: "Azure Function App は、Azure IoT ハブのイベントをリッスンして受信メッセージを処理し、それらを Azure Table Storage に書き込みます。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "クラウドへのデータの格納, クラウドに格納されているデータ, IoT クラウド サービス"
ms.assetid: 739b82e9-5d4e-4485-8971-f57cbb682faf
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 2c2f540a0000851fc5ea50ef28f559d19fc8357f


---
# <a name="create-an-azure-function-app-and-azure-storage-account"></a>Azure Function App と Azure Storage アカウントの作成
[Azure Functions](../../articles/azure-functions/functions-overview.md) は、"*関数*" (小規模なコード) をクラウドで手軽に実行するためのソリューションです。 Azure Function App は、Azure での関数の実行をホストします。

## <a name="what-will-you-do"></a>作業内容
Azure Resource Manager テンプレートを使用して、Azure Function App と Azure Storage アカウントを作成します。 Azure Function App は、Azure IoT ハブのイベントをリッスンして受信メッセージを処理し、それらを Azure Table Storage に書き込みます。 ストレージ アカウントは、Azure テーブルからメッセージの永続コピーを読み取るために使用されます。 問題が発生した場合は、[トラブルシューティングのページ][troubleshooting]で解決策をお探しください。

## <a name="what-will-you-learn"></a>学習内容
この記事では、次のことについて説明します。
* [Azure Resource Manager](../../articles/azure-resource-manager/resource-group-overview.md) を使用して Azure リソースをデプロイする方法。
* Azure Function App を使用し、IoT ハブのメッセージを処理して Azure Table Storage のテーブルに書き込む方法。

## <a name="what-do-you-need"></a>必要なもの
次の作業を完了している必要があります。
- [Intel Edison の使用][get-started-with-your-intel-edison]
- [Azure IoT Hub の作成][create-your-azure-iot-hub]

## <a name="open-the-sample-app"></a>サンプル アプリを開く
次のコマンドを実行して、Visual Studio Code でサンプル プロジェクトを開きます。

```bash
cd Lesson3
code .
```

![リポジトリの構造][repo-structure]

* `app` サブフォルダーのファイルは、キー ソース ファイルです。 このソース ファイルには、メッセージを IoT ハブに 20 回送信し、メッセージを送信するたびに LED を点滅させるコードが含まれています。
* `arm-template.json` ファイルは、Azure Function App と Azure Storage アカウントが含まれた Azure Resource Manager テンプレートです。
* `arm-template-param.json` ファイルは、Azure Resource Manager テンプレートによって使用される構成ファイルです。
* `ReceiveDeviceMessages` サブフォルダーは、Azure 関数の Node.js コードが含まれています。

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Azure Resource Manager テンプレートの構成と Azure でのリソースの作成
Visual Studio Code で `arm-template-param.json` ファイルを更新します。

![Azure Resource Manager テンプレートのパラメーター][arm-template-parameters]

* **[your IoT Hub name]** を、[IoT ハブを作成して Intel Edison を登録][created-your-iot-hub-and-registered-intel-edison]したときに指定した **{ハブ名}** で置き換えます。
* **[prefix string for new resources]** を任意のプレフィックスで置き換えます。 このプレフィックスによって、必ずリソース名をグローバルに一意にでき、競合を避けることができます。 ダッシュまたは番号のイニシャルは、プレフィックスに使用しないようにしてください。

`arm-template-param.json` ファイルの更新後、次のコマンドを実行してリソースを Azure にデプロイします。

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-sample
```

これらのリソースの作成には約&5; 分かかります。 リソースの作成の進行中に、次の記事に進むことができます。

## <a name="summary"></a>概要
IoT ハブのメッセージを処理する Azure Function App と、それらのメッセージを格納する Azure Storage アカウントを作成しました。 デバイスからクラウドへのメッセージを送信するサンプルを Edison にデプロイして実行できます。

## <a name="next-steps"></a>次のステップ
[Intel Edison での、デバイスからクラウドへのメッセージを送信するサンプル アプリケーションの実行][send-device-to-cloud-messages]。
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[get-started-with-your-intel-edison]: iot-hub-intel-edison-kit-c-get-started.md
[create-your-azure-iot-hub]: iot-hub-intel-edison-kit-c-get-started.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson3/repo_structure_c.png
[arm-template-parameters]: /media/iot-hub-intel-edison-lessons/lesson3/arm_para_c.png
[created-your-iot-hub-and-registered-intel-edison]: iot-hub-intel-edison-kit-c-lesson2-prepare-azure-iot-hub.md
[send-device-to-cloud-messages]: iot-hub-intel-edison-kit-c-lesson3-run-azure-blink.md


<!--HONumber=Jan17_HO4-->


