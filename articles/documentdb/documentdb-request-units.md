---
title: "要求ユニットとスループットの推定 - Azure DocumentDB | Microsoft Docs"
description: "DocumentDB の要求ユニット要件を確認、指定、推定する方法について説明します。"
services: documentdb
author: syamkmsft
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: d0a3c310-eb63-4e45-8122-b7724095c32f
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: syamk
translationtype: Human Translation
ms.sourcegitcommit: a6aadaae2a9400dc62ab277d89d9a9657833b1b7
ms.openlocfilehash: bf58d333e81fb76ffc3cca8a8e1ccb3f71ac72c9


---
# <a name="request-units-in-documentdb"></a>DocumentDB の要求ユニット
DocumentDB の [要求ユニット計算ツール](https://www.documentdb.com/capacityplanner)が新たに公開されました。 詳細については、「 [スループットのニーズの推定](documentdb-request-units.md#estimating-throughput-needs)」を参照してください。

![Throughput calculator][5]

## <a name="introduction"></a>はじめに
この記事では、 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)の要求ユニットの概要について説明します。 

この記事を読むと、次の質問に回答できるようになります。  

* 要求ユニットと要求の使用量とは何ですか。
* コレクションの要求ユニットの量をどのように指定しますか。
* アプリケーションの要求ユニットのニーズをどのように推定しますか。
* コレクションの要求ユニットの量を超過すると、どうなりますか。

## <a name="request-units-and-request-charges"></a>要求ユニットと要求の使用量
DocumentDB を使用して、アプリケーションのスループット ニーズを満たすリソースを *予約する* ことで、高速の予測可能なパフォーマンスが得られます。  アプリケーション負荷とアクセス パターンは時間と共に変化するため、DocumentDB はアプリケーションで利用できる予約済みスループットの量を簡単に増減できる機能を備えています。

DocumentDB では、1 秒間に処理する要求ユニットで、予約済みスループットを指定します。  要求ユニットはスループットの通貨と考えることができます。この通貨によって、アプリケーションで利用できる保証された要求ユニット量を秒単位で "*予約*" できます。  ドキュメントの作成、クエリの実行、ドキュメントの更新など、DocumentDB での各操作は、CPU、メモリ、IOPS を消費します。  つまり、それぞれの操作により、"*要求の使用量*" が発生し、それが "*要求ユニット*" で表されます。  アプリケーションのスループット要件と共に、要求ユニット使用量に影響を及ぼす要因を理解しておくと、できるだけコスト効率の高い方法でアプリケーションを実行できます。 

まずは以下のビデオを見ることをお勧めします。このビデオでは、Aravind Ramachandran が要求ユニットと DocumentDB での予測可能なパフォーマンスについて説明しています。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Predictable-Performance-with-DocumentDB/player]
> 
> 

## <a name="specifying-request-unit-capacity"></a>要求ユニット量の指定
DocumentDB コレクションの作成時に、そのコレクション用に予約する&1; 秒ごとの要求ユニット数 (RU) を指定します。  コレクションを作成した後、そのコレクションで使用するために、指定された RU の完全な割り当てが予約されます。  各コレクションは、専用の分離されたスループット特性を持つことが保証されます。  

DocumentDB は予約モデルで運用されることに注意してください。つまり、実際に "*使用した*" スループット量ではなく、コレクション用に "*予約した*" スループット量に対する料金が請求されます。  ただし、アプリケーションの負荷やデータ、使用パターンが変化したときに、DocumentDB SDK または [Azure ポータル](https://portal.azure.com)で、予約済み RU の量を簡単にスケールアップまたはスケールダウンできます。  スループットのスケールアップとスケールダウンの詳細については、「[DocumentDB のパフォーマンス レベル](documentdb-performance-levels.md)」を参照してください。

## <a name="request-unit-considerations"></a>要求ユニットの考慮事項
DocumentDB コレクション用に予約する要求ユニット数を推定する際は、以下の変数についても検討することが重要です。

* **ドキュメント サイズ**。 ドキュメントのサイズが増大すると、データの読み取りまたは書き込みに使用される単位も増加します。
* **ドキュメント プロパティの数**。 すべてのプロパティに既定でインデックスが作成されると想定すると、プロパティ数の増加に伴って、ドキュメントの書き込みに使用される単位が増加します。
* **データ整合性**。 Strong または Bounded Staleness のデータの整合性レベルを使用すると、ドキュメントの参照に使用される単位が増加します。
* **インデックス付きプロパティ**。 各コレクションのインデックス ポリシーによって、既定でインデックスが作成されるプロパティが決まります。 インデックス付きプロパティの数を制限するか、非同期インデックス作成を有効にして、使用する要求ユニットを削減できます。
* **ドキュメントのインデックス作成**。 各ドキュメントには既定で自動的にインデックスが作成されます。一部のドキュメントにインデックスを作成しないようにすると、使用される要求単位が少なくなります。
* **クエリのパターン**。 クエリの複雑さは、操作で消費される要求ユニット数に影響します。 述語の数、述語の特性、プロジェクション、UDF 数、ソース データ セットのサイズのすべてが、クエリ操作のコストに影響します。
* **スクリプトの使用**。  クエリと同様に、ストアド プロシージャとトリガーは、実行する操作の複雑さに基づいて要求ユニットを使用します。 アプリケーションを開発するときは、ヘッダーを調べて、各操作で使用される要求単位の量を詳しく把握するようにしてください。

## <a name="estimating-throughput-needs"></a>スループットのニーズの推定
要求単位は、要求の処理コストを標準化した測定単位です。 1 つの要求ユニットは、10 個の一意のプロパティ値 (システム プロパティを除く) で構成される 1 KB JSON ドキュメントの読み取り (self リンクまたは ID による) に必要な処理能力を表します。 同じドキュメントを作成 (挿入)、置換、または削除する要求では、必要になるサービスの処理が多くなるため、使用される要求ユニットも多くなります。   

> [!NOTE]
> 1 KB のドキュメントに対する 1 つの要求ユニットのベースラインは、ドキュメントの self リンクまたは ID による単純な GET に対応します。
> 
> 

### <a name="use-the-request-unit-calculator"></a>要求ユニット計算ツールの使用
スループットをユーザーが緻密に推定できるように、Web ベースの[要求ユニット計算ツール](https://www.documentdb.com/capacityplanner)が用意されています。たとえば以下に示す標準的な操作で必要になる要求ユニットを見積もることができます。

* ドキュメントの作成 (書き込み)
* ドキュメントの読み取り
* ドキュメントの削除
* ドキュメントの更新

このツールには、指定したサンプル ドキュメントに基づいたデータ ストレージ ニーズの見積もりのサポートも含まれています。

ツールの使い方は簡単です。

1. 代表的な JSON ドキュメントを少なくとも&1; つアップロードします。
   
    ![Upload documents to the request unit calculator][2]
2. データ ストレージの要件を見積もるには、格納するドキュメントの総数を入力します。
3. ドキュメントの作成、読み取り、更新、削除に関して必要な操作数 (毎秒あたり) を入力します。 ドキュメントの更新操作の要求ユニットの料金を見積もるには、上記の手順 1. のサンプル ドキュメントのうち、代表的なフィールドの更新が含まれているドキュメントのコピーをアップロードします。  たとえば、ドキュメントの更新では、通常、lastLogin と userVisits という&2; つのプロパティだけを変更する場合は、サンプル ドキュメントをコピーし、その&2; つのプロパティの値を更新して、コピーしたドキュメントをアップロードします。
   
    ![Enter throughput requirements in the request unit calculator][3]
4. 計算ボタンをクリックして結果を確認します。
   
    ![Request unit calculator results][4]

> [!NOTE]
> ドキュメントの種類によって、サイズとインデックス付きプロパティの数が大きく異なる場合は、それぞれの *種類* における代表的なドキュメントのサンプルをツールにアップロードしたうえで、結果を計算してください。
> 
> 

### <a name="use-the-documentdb-request-charge-response-header"></a>DocumentDB の "要求の使用量" 応答ヘッダーの使用
DocumentDB サービスからの各応答には、要求で使用される要求ユニットを含むカスタム ヘッダー (x-ms-request-charge) が付きます。 このヘッダーには、DocumentDB SDK を介してアクセスすることもできます。 .NET SDK では、RequestCharge は ResourceResponse オブジェクトのプロパティです。  Azure ポータルの DocumentDB クエリ エクスプローラーは、実行されたクエリに関する要求の使用量情報を示します。

![Examining RU charges in the Query Explorer][1]

これを踏まえて、アプリケーションに必要な予約済みスループットの量を推定するには、典型的な操作の実行に関連する要求ユニット使用量を記録し、アプリケーションが使用する代表的なドキュメントに基づいて、1 秒ごとに実行される操作数を推定します。  さらに、通常のクエリと DocumentDB スクリプトの使用も忘れずに測定し、考慮に入れます。

> [!NOTE]
> ドキュメントの種類によって、サイズとインデックス付きプロパティの数が大きく異なる場合は、典型的なドキュメントの *種類* ごとに、適用可能な操作の要求ユニット使用量を記録してください。
> 
> 

次に例を示します。

1. 典型的なドキュメント作成 (挿入) の要求ユニット使用量を記録します。 
2. 典型的なドキュメント読み取りの要求ユニット使用量を記録します。
3. 典型的なドキュメント更新の要求ユニット使用量を記録します。
4. 典型的な共通ドキュメントのクエリの要求ユニット使用量を記録します。
5. アプリケーションが利用するカスタム スクリプト (ストアド プロシージャ、トリガー、ユーザー定義関数) の要求ユニット使用量を記録します。
6. 予想される&1; 秒あたりの操作実行数の推定値に基づいて、必要な要求ユニットを計算します。

## <a name="a-request-unit-estimation-example"></a>要求ユニット推定の例
次の&1; KB 未満のドキュメントについて考えてみましょう。

    {
     "id": "08259",
      "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
      "tags": [
        {
          "name": "cereals ready-to-eat"
        },
        {
          "name": "kellogg"
        },
        {
          "name": "kellogg's crispix"
        }
    ],
      "version": 1,
      "commonName": "Includes USDA Commodity B855",
      "manufacturerName": "Kellogg, Co.",
      "isFromSurvey": false,
      "foodGroup": "Breakfast Cereals",
      "nutrients": [
        {
          "id": "262",
          "description": "Caffeine",
          "nutritionValue": 0,
          "units": "mg"
        },
        {
          "id": "307",
          "description": "Sodium, Na",
          "nutritionValue": 611,
          "units": "mg"
        },
        {
          "id": "309",
          "description": "Zinc, Zn",
          "nutritionValue": 5.2,
          "units": "mg"
        }
      ],
      "servings": [
        {
          "amount": 1,
          "description": "cup (1 NLEA serving)",
          "weightInGrams": 29
        }
      ]
    }

> [!NOTE]
> ドキュメントは DocumentDB で縮小されるため、システムで計算される上記ドキュメントのサイズは 1 KB をわずかに下回ります。
> 
> 

次の表は、このドキュメントに対する典型的な操作に要する、大まかな要求ユニット使用量を示しています (大まかな要求ユニット使用量は、アカウント一貫性レベルが "セッション" に設定され、すべてのドキュメントに自動でインデックス作成が行われることを想定しています)。

| 操作 | 要求ユニット使用量 |
| --- | --- |
| ドキュメントの作成 |～15 RU |
| ドキュメントの読み取り |～1 RU |
| ID でのドキュメントのクエリ |～2.5 RU |

さらに次の表では、アプリケーションで通常使用されるクエリについて、大まかな要求ユニット使用量を示しています。

| クエリ | 要求ユニット使用量 | # 返されるドキュメント数 |
| --- | --- | --- |
| ID で食品を選択 |～2.5 RU |1 |
| 製造者で食品を選択 |～7 RU |7 |
| 食品グループで選択し、重量順に配列 |～70 RU |100 |
| 食品グループの上位 10 食品を選択 |～10 RU |10 |

> [!NOTE]
> RU 使用量は返されるドキュメントの数により異なります。
> 
> 

この情報があれば、予測される&1; 秒あたりの操作数とクエリ数に基づいて、このアプリケーションに必要な RU 要件を推定できます。

| 操作/クエリ | 1 秒あたりの推定数 | 必要な RU |
| --- | --- | --- |
| ドキュメントの作成 |10 |150 |
| ドキュメントの読み取り |100 |100 |
| 製造者で食品を選択 |25 |175 |
| 食品グループで選択 |10 |700 |
| 上位 10 を選択 |15 |合計&150; |

この例では、平均スループット要件を 1,275 RU/s と想定しています。  100 の位で丸めて、このアプリケーションのコレクションに 1,300 RU/s をプロビジョニングすることになります。

## <a name="a-idrequestratetoolargea-exceeding-reserved-throughput-limits"></a><a id="RequestRateTooLarge"></a> 予約されたスループット上限の超過
要求ユニットの消費は、1 秒あたりのレートとして評価されることを思い出してください。 コレクションに対してプロビジョニングされた要求単位レートをアプリケーションが超過した場合、そのレートが予約されたレベルを下回るまで、コレクションに対する要求は制限されます。 スロットルが発生すると、サーバーはいち早く RequestRateTooLargeException (HTTP 状態コード 429) で要求を終了させ、x-ms-retry-after-ms ヘッダーを返して、ユーザーが要求の試行を再開できるまでに待機しなければならない時間をミリ秒で示します。

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

.NET クライアント SDK と LINQ クエリ使用していると、ほとんどの場合は、この例外に対処する必要がありません。なぜなら、最新バージョンの .NET クライアント SDK は暗黙的にこの応答を取得し、サーバーが規定した retry-after ヘッダーを考慮して、要求を再試行するからです。 アカウントに複数のクライアントが同時アクセスしている状況でなければ、次回の再試行は成功します。

複数のクライアントが上述の要求レートで累積的に操作を実行している場合は、既定の再試行動作では十分な結果が得られず、クライアントは状態コード 429 で DocumentClientException をアプリケーションにスローします。 こうした場合は、再試行動作とアプリケーションのエラー処理ルーチンのロジックを制御するか、対象コレクションの予約済みスループットを増やすことを検討します。

## <a name="next-steps"></a>次のステップ
Azure DocumentDB データベースの予約済みスループットの詳細については、以下のリソースを参照してください。

* [DocumentDB の料金](https://azure.microsoft.com/pricing/details/documentdb/)
* [DocumentDB のデータのモデル化](documentdb-modeling-data.md)
* [DocumentDB のパフォーマンス レベル](documentdb-partition-data.md)

DocumentDB の詳細については、Azure DocumentDB に関する [ドキュメント](https://azure.microsoft.com/documentation/services/documentdb/)を参照してください。 

DocumentDB に関するスケールとパフォーマンスのテストを始めるには、「 [Azure DocumentDB のパフォーマンスとスケールのテスト](documentdb-performance-testing.md)」をご覧ください。

[1]: ./media/documentdb-request-units/queryexplorer.png 
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png



<!--HONumber=Jan17_HO4-->


