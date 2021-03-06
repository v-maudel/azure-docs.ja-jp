---
title: "Azure Batch サービスの概要 | Microsoft Docs"
description: "大規模な並列ワークロードと HPC ワークロードに関する Azure Batch サービスの使用方法について説明します。"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/23/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: bf22cd3426e936c8d74377f59443e5e1a6834286
ms.openlocfilehash: 3a01764b6a0e777e947cf8648606cdd568d118ff


---
# <a name="basics-of-azure-batch"></a>Azure Batch の基礎
Azure Batch は、大規模な並列コンピューティングやハイ パフォーマンス コンピューティング (HPC) のアプリケーションをクラウドで効率的に実行するためのプラットフォーム サービスです。 多くのコンピューティング処理を要する作業を管理された仮想マシンの集合で実行するようにスケジュール設定し、ジョブのニーズに合わせてコンピューティング リソースを自動的に拡大/縮小できます。

Azure Batch では、複数のアプリケーションを並列で大規模に実行するための Azure コンピューティング リソースを簡単に定義できます。 HPC クラスター、個々の仮想マシン、仮想ネットワークや、複雑なジョブとタスクのスケジュール インフラストラクチャを手動で作成、構成、管理する必要がありません。 Azure Batch によって、これらのタスクが自動化または簡略化されます。

## <a name="use-cases-for-batch"></a>Batch の使用例
Batch は、"*バッチ処理*" または "*バッチ コンピューティング*" に使用される管理された Azure サービスであり、期待した結果が得られるように類似のタスクを大量に実行します。 バッチ コンピューティングは主に、大量のデータを定期的に処理し、変換し、分析する組織で使用されます。

Batch は本質的に並列 (「驚異的並列」とも呼ばれています) のアプリケーションとワークロードと効果的に連動します。 本質的に、並列のワークロードは、複数のタスクに簡単に分割されるワークロードです。複数に分割されたタスクは複数のコンピューターで同時に実行されます。

![並列タスク][1]<br/>

この手法で一般的に処理されるワークロードの例には次のものがあります。

* 財務リスクのモデリング
* 気候や水文学データの分析
* イメージの表示、分析、処理
* メディアのエンコードとコード変換
* 遺伝子配列の分析
* 工学応力の分析
* ソフトウェアのテスト

また、Batch を使用すると、最終的により少ないステップで並列計算を実行できるほか、 [Message Passing Interface (MPI)](batch-mpi.md) アプリケーションなどのより複雑な HPC ワークロードを実行できます。

Batch と Azure 内の他の HPC ソリューション オプションとの比較については、「 [Batch と HPC ソリューション](batch-hpc-solutions.md)」を参照してください。

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="developing-with-batch"></a>Batch を使用した開発
Azure Batch による並列ワークロードの処理は、通常、[Batch API](#batch-development-apis) のいずれかを使用して、プログラムで実行されます。 クライアント アプリケーションまたはサービスは、Batch API を使用して Batch サービスと通信することができます。 Batch API を使用すると、コンピューティング ノード (仮想マシンまたはクラウド サービス) のプールを作成して管理できます。 その後、それらのノードで実行するジョブおよびタスクをスケジュールできます。 

組織の大規模なワークロードを効率的に処理したり、ノードの数を問わず (1 つ、数百、数千など)、必要なときに、またはスケジュールに基づいてジョブやタスクを実行できるように、顧客にサービス フロントエンドを提供したりできます。 また、Azure Batch を大規模なワークフローの一部として使用し、[Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md) などのツールで管理できます。

> [!TIP]
> Batch API の機能をもっと詳しく理解するには、「 [開発者向け Batch 機能の概要](batch-api-basics.md)」をご覧ください。
> 
> 

### <a name="azure-accounts-youll-need"></a>必要となる Azure アカウント
Batch ソリューションを開発するとき、Microsoft Azure で次のアカウントが必要になります。

* **Azure アカウントとサブスクリプション** - Azure サブスクリプションを持っていない場合は、[MSDN サブスクライバーの特典][msdn_benefits]を有効にするか、[無料 Azure アカウント][free_account]にサインアップしてください。 アカウントを作成すると、既定のサブスクリプションが自動的に作成されます。
* **Batch アカウント** - Azure Batch リソース (プール、コンピューティング ノード、ジョブ、タスクなど) は、Azure Batch アカウントに関連付けられています。 アプリケーションは、Batch サービスに対する要求を行う際に、Azure Batch アカウント名、アカウントの URL、およびアクセス キーを使用して要求を認証します。 Azure Portal で [Batch アカウントを作成](batch-account-create-portal.md) できます。
* **ストレージ アカウント** - Batch には、[Azure Storage][azure_storage] のファイルを操作するためのサポートが組み込まれています。 ほぼすべての Batch シナリオで、Azure Blob Storage が使用されます。タスクで実行されるプログラムや、プログラムで処理されるデータのステージングに使用されたり、プログラムで生成される出力データの格納に使用されたりします。 ストレージ アカウントの作成方法については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。

### <a name="batch-development-apis"></a>Batch 開発 API
アプリケーションとサービスは、直接 REST APIを呼び出したり、以下の&1; つ以上のクライアント ライブラリを使用したりして、Azure Batch ワークロードを実行および管理することができます。

| API | API リファレンス | ダウンロード | チュートリアル | コード サンプル |
| --- | --- | --- | --- | --- |
| **Batch REST** |[MSDN][batch_rest] |該当なし |- |- |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[チュートリアル](batch-dotnet-get-started.md) |[GitHub][api_sample_net] |
| **Batch Python** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[チュートリアル](batch-python-tutorial.md)|[GitHub][api_sample_python] |
| **Batch Node.js** |[github.io][api_nodejs] |[npm][api_nodejs_npm] |- |- |
| **Batch Java** (プレビュー) |[github.io][api_java] |[Maven][api_java_jar] |- |[GitHub][api_sample_java] |

### <a name="batch-command-line-tools"></a>Batch コマンド ライン ツール

開発 API で用意されている機能は、コマンド ライン ツールを使用して利用することもできます。 

* [Batch PowerShell コマンドレット][batch_ps]: [Azure PowerShell](/powershell/azureps-cmdlets-docs) モジュールの Azure Batch コマンドレットを利用すると、PowerShell で Batch リソースを管理できます。
* [Azure CLI](../xplat-cli-install.md): Azure コマンド ライン インターフェイス (Azure CLI) は、Batch などの多くの Azure サービスを操作するためのシェル コマンドを提供するクロスプラットフォーム ツールセットです。

### <a name="batch-resource-management"></a>Batch リソース管理

Batch 用の Azure Resource Manager API には、Batch アカウントにプログラムでアクセスする機能が用意されています。 これらの API を使用すると、Batch アカウント、クォータ、およびアプリケーション パッケージをプログラムで管理できます。  

| API | API リファレンス | ダウンロード | チュートリアル | コード サンプル |
| --- | --- | --- | --- | --- |
| **Batch Resource Manager REST** |[docs.microsoft.com][api_rest_mgmt] |該当なし |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Resource Manager .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [チュートリアル](batch-management-dotnet.md) |[GitHub][api_sample_net] |


### <a name="batch-tools"></a>Batch ツール
Batch を使用してソリューションを構築する必要はありませんが、Batch アプリケーションおよびサービスのビルドとデバッグの際に役立つツールがいくつかあります。

* [Azure Portal][portal]: Azure Portal の Batch ブレードでは、Batch プール、ジョブ、タスクを作成、監視、削除できます。 ジョブの実行時にこれらをはじめとする各種リソースの状態情報を確認できるほか、プールのコンピューティング ノードからファイルをダウンロードすることもできます (トラブルシューティングの際に、失敗したタスクの `stderr.txt` をダウンロードするなど)。 コンピューティング ノードへのログインに使用できるリモート デスクトップ (RDP) ファイルをダウンロードすることもできます。
* [Azure Batch Explorer][batch_explorer]: Batch Explorer には Azure Portal と同様の Batch リソース管理機能がありますが、これはスタンドアロンの Windows Presentation Foundation (WPF) クライアント アプリケーションの形で提供されます。 [GitHub][github_samples] で提供されている Batch .NET サンプル アプリケーションの 1 つであり、Visual Studio 2015 以上でビルドできます。これを使うと、Batch ソリューションの開発とデバッグの際に、Batch アカウントのリソースを参照、管理できます。 ジョブ、プール、タスクの詳細を表示したり、コンピューティング ノードからファイルをダウンロードしたりできます。また、Batch Explorer を使ってダウンロードできるリモート デスクトップ (RDP) ファイルを利用してノードにリモート接続することもできます。
* [Microsoft Azure ストレージ エクスプローラー][storage_explorer]: 厳密には Azure Batch ツールではありませんが、ストレージ エクスプローラーは Batch ソリューションの開発とデバッグで役に立つツールです。

## <a name="scenario-scale-out-a-parallel-workload"></a>シナリオ: 並列ワークロードをスケールアウトする
Batch API を利用し、Batch サービスとやりとりする一般的なソリューションには、コンピューティング ノードのプールでの本質的に並列な作業の拡張があります。3D シーンのイメージのレンダリングなどです。 このようなコンピューティング ノードのプールは「レンダー ファーム」となり、たとえば、数十、数百、さらには数千のコアをレンダリング ジョブに提供します。

次の図は一般的な Batch ワークフローを示しています。クライアント アプリケーションまたはホステッド サービスが Batch を利用し、並列ワークロードを実行します。

![Batch ソリューション ワークフロー][2]

この一般的なシナリオで、アプリケーションまたはサービスは、次の手順を実行することで Azure Batch のコンピューティング ワークロードを処理します。

1. **入力ファイル**とそのファイルを処理する**アプリケーション**を Azure ストレージ アカウントにアップロードします。 アプリケーションが処理するあらゆるデータが入力ファイルになります。財務モデリング データや変換する動画ファイルなどです。 データを処理するためのあらゆるアプリケーションがアプリケーション ファイルになります。3D レンダリング アプリケーションやメディア トランスコーダーなどです。
2. Batch アカウントでコンピューティング ノードの Batch **プール**を作成します。これらのノードは、タスクを実行する仮想マシンです。 [ノード サイズ](../cloud-services/cloud-services-sizes-specs.md)、オペレーティング システム、ノードがプールに参加するときにインストールするアプリケーション (手順 1. でアップロードしたアプリケーション) の Azure Storage における場所などのプロパティを指定します。 タスクによって生成されるワークロードに応じて、[自動的にスケール](batch-automatic-scaling.md)するようにプールを構成することもできます。 自動スケールでは、プール内のコンピューティング ノードの数が動的に調整されます。
3. コンピューティング ノードのプールでワークロードを実行する Batch **ジョブ** を作成します。 ジョブを作成するとき、ジョブを Batch プールに関連付けます。
4. ジョブに **タスク** を追加します。 ジョブにタスクを追加すると、Batch サービスは、プールのコンピューティング ノードでタスクを実行するように自動的にスケジュールします。 各タスクでアップロードしたアプリケーションが使用され、入力ファイルが処理されます。
   
   * 4a. タスクが実行される前に、割り当てられているコンピューティング ノードに処理するデータ (入力ファイル) をダウンロードできます。 アプリケーションがノードにインストールされていない場合 (手順 2 参照)、代わりにここでダウンロードできます。 ダウンロードが完了すると、割り当てられているノードでタスクが実行されます。
5. タスクが実行されたら、Batch にクエリを実行し、ジョブとそのタスクの進捗状況を監視できます。 クライアント アプリケーションまたはサービスは、HTTPS 経由で Batch サービスと通信します。 数千のコンピューティング ノードで実行されている数千のタスクを監視している場合があるため、[Batch サービスに対して効率的にクエリを実行する](batch-efficient-list-queries.md)ようにしてください。
6. タスクが完了すると、結果データを Azure Storage にアップロードできます。 コンピューティング ノードのファイル システムからファイルを直接取得することもできます。
7. ジョブのタスクが完了したことが監視により検出された場合、クライアント アプリケーションまたはサービスで出力データをダウンロードし、さらに処理したり、評価したりできます。

これは Batch の利用方法の&1; つにすぎません。このシナリオでは、利用できる機能のほんの一部のみを紹介しています。 たとえば、各コンピューティング ノードで[複数のタスクを並列に](batch-parallel-node-tasks.md)実行できます。また、[ジョブの準備と完了のタスク](batch-job-prep-release.md)を利用してジョブのノードを準備し、後で消去できます。

## <a name="next-steps"></a>次のステップ
これで Batch サービスの概要を確認できました。次は、深く掘り下げ、コンピューティング リソースが大量に使われる並列ワークロードをサービスで処理する方法を学習してください。

* Batch を使用するための準備を担当する方は、「 [開発者向け Batch 機能の概要](batch-api-basics.md)」で重要な情報をご確認ください。 この記事には、Batch アプリケーションを構築するときに使用できる多数の API 機能、プール、ノード、ジョブ、タスクなど、Batch サービスのリソースに関する詳しい情報が記載されています。
* [.NET 向け Azure Batch ライブラリの概要](batch-dotnet-get-started.md) 」では、C# と Batch .NET ライブラリを利用し、一般的な Batch ワークフローを使用して簡単なワークロードを実行する方法を学習できます。 この記事は、Batch サービスの使用方法を学習するときに最初に参考していただきたい記事の&1; つです。 また、 [Python 向け](batch-python-tutorial.md) のチュートリアルも用意されています。
* [GitHub のサンプル コード][github_samples]をダウンロードし、C# と Python の両方について、Batch とやり取りしてサンプル ワークロードのスケジュール設定と処理を実行する方法を確認してください。
* [Batch ラーニング パス][learning_path]で、Batch の使用方法を学習する際に利用できるリソースを確認してください。

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: https://docs.microsoft.com/en-us/rest/api/batchmanagement/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png



<!--HONumber=Jan17_HO4-->


