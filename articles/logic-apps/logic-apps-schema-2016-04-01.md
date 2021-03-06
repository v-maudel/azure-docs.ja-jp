---
title: "新しいスキーマ バージョン 2016-06-01 | Microsoft Docs"
description: "最新バージョンのロジック アプリの JSON 定義を記述する方法について説明します。"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: aaacb76fe845ca8892e4fe3979be5ea6ac5e902b


---
# <a name="new-schema-version-2016-06-01"></a>新しいスキーマ バージョン 2016-06-01
ロジック アプリの新しいバージョンのスキーマと API には、ロジック アプリの信頼性と使いやすさを向上させる複数の機能強化が行われています。 主な相違点は次の 3 つです。

1. スコープ (一連のアクションを含んだアクション) の追加。
2. 条件とループがアクションに昇格。
3. 実行の順序を `runAfter` プロパティ (`dependsOn` の後継) でより柔軟に定義可能。

2015-08-01-preview スキーマから 2016-06-01 スキーマへのロジック アプリのアップグレードについては、 [以降のアップグレード セクションを参照](#upgrading-to-2016-06-01-schema)

## <a name="1-scopes"></a>1.スコープ
このスキーマの最も大きな変更の&1; つは、スコープが追加されてアクションを入れ子にできるようになったことです。  複数のアクションを&1; つにまとめるときや、アクションを入れ子にする必要があるときにスコープを利用できます (たとえば特定の条件に別の条件を含めることが可能)。  スコープの構文について詳しくは、[こちら](../logic-apps/logic-apps-loops-and-scopes.md)を参照してください。以下に示したのは単純なスコープの例です。

```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

## <a name="2-conditions-and-loops-changes"></a>2.条件とループに関する変更
以前のバージョンのスキーマでは条件とループが、パラメーターとして単一のアクションに関連付けられていました。  新しいスキーマではこの制限が廃止され、条件とループはアクションのタイプとして記述されます。  詳細については、[こちらの記事](../logic-apps/logic-apps-loops-and-scopes.md)を参照してください。以下に示したのは、条件アクションの単純な例です。

```
{
    "If_trigger_is_foo": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'foo')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_bar": "..."
        }      
    }
}
```

## <a name="3-runafter-property"></a>3.RunAfter プロパティ
実行順序をより正確に指定できるように、`dependsOn` が新しい `runAfter` プロパティに置き換えられます。  `dependsOn` は、"アクションの実行に成功した場合" という意味ですが、直前のアクションの結果 (成功、失敗、スキップ) に応じてアクションを実行しなければならないケースは少なくありません。  `runAfter` です。  特定の条件が満たされた後に実行されるすべてのアクション名をこのオブジェクトで指定し、トリガーの条件となるステータスの配列を定義します。  たとえばステップ A とステップ B が成功した後に実行したい場合は、 `runAfter` プロパティを次のように記述します。

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrading-to-2016-06-01-schema"></a>2016-06-01 スキーマへのアップグレード
新しい 2016-06-01 スキーマへのアップグレードは、ほんのわずかなステップで実行できます。  スキーマの変更点について詳しくは、 [こちらの記事](../logic-apps/logic-apps-schema-2016-04-01.md)をご覧ください。  アップグレード プロセスでは、アップグレード スクリプトの実行と新しいロジック アプリの保存に加え、必要に応じて以前のロジック アプリの上書きを行います。

1. 現在のロジック アプリを開きます。
2. ツール バーにある **[スキーマの更新]** ボタンをクリックします。
   
    ![][1]
   
    アップグレード後の定義が返されます。  これを必要に応じてコピーしてリソース定義に貼り付けてもかまいませんが、アップグレード後のロジック アプリに含まれるすべての接続の参照の有効性を確保するために、**[名前を付けて保存]** ボタンを使用することを**強くお勧めします**。
3. アップグレード ブレードのツール バーにある **[名前を付けて保存]** ボタンをクリックします。
4. 名前とロジック アプリのステータスを入力して **[作成]** をクリックし、アップグレードしたロジック アプリをデプロイします。
5. アップグレードしたロジック アプリが正常に動作していることを確認します。
   
   > [!NOTE]
   > 手動トリガーまたは要求トリガーを使用している場合、新しいロジック アプリのコールバック URL が変更されます。  その新しい URL を使用して、エンド ツー エンドで正常に動作することを確認したら、既存のロジック アプリ上に複製して以前の URL を維持することができます。
   > 
   > 
6. *(省略可能)* ツール バーの **[複製]** ボタン (上図の **[スキーマの更新]** アイコンの横) を使用して、以前のロジック アプリを新しいスキーマ バージョンで上書きします。  この手順は、ロジック アプリのリソース ID または要求トリガー URL をそのまま維持したい場合に必要となります。

### <a name="upgrade-tool-notes"></a>アップグレード ツールの注意事項
#### <a name="condition-mapping"></a>条件のマッピング
このツールは、可能な限り条件分岐アクションを&1; つのスコープにまとめたうえで、定義をアップグレードするように試みます。  具体的には、`@equals(actions('a').status, 'Skipped')` というデザイナー パターンは、`else` アクションとしてアップグレードされます。  ただし認識できないパターンが検出された場合は、true の分岐と false の分岐について別個の条件が作成される可能性があります。  アップグレード後、必要に応じてアクションのマッピングを変更してください。

#### <a name="foreach-with-condition"></a>条件判定を伴う ForEach
アイテム単位の条件判定を伴う foreach ループの従来のパターンは、新しいスキーマの中でフィルター アクションとして再現されます。  この処置は通常、アップグレード時に自動的に行われます。  このような条件は、foreach ループに先行するフィルター アクションに置き換えられます。つまり、このフィルター アクションによって条件に一致したアイテムのみの配列が取得され、foreach アクションに渡されます。  具体的な例については、[こちらの記事](../logic-apps/logic-apps-loops-and-scopes.md)を参照してください。

#### <a name="resource-tags"></a>リソース タグ
リソース タグはアップグレード時に削除されるので、アップグレード後のワークフローで設定し直す必要があります。

## <a name="other-changes"></a>その他の変更点
### <a name="manual-trigger-renamed-to-request-trigger"></a>手動トリガーの名前を要求トリガーに変更
`manual` タイプは廃止予定となり、種類が `http` である `request` に名前が変更されました。  これにより、トリガーを使用して構築されるパターンのタイプとの一貫性が向上しています。

### <a name="new-filter-action"></a>新しい 'フィルター' アクション
大きな配列を扱っているとき、その要素をフィルタリングして配列のサイズを小さくする必要がある場合に、今後は 'filter' という新しいタイプを使用できます。  配列と条件を渡すと、その条件と照らして個々の要素が評価され、条件を満たした要素の配列が返されます。

### <a name="foreach-and-until-action-restrictions"></a>ForEach と until アクションの制限
foreach と until ループは、1 つのアクションに制限されます。

### <a name="trackedproperties-on-actions"></a>アクションの TrackedProperties
アクションに `trackedProperties` というプロパティを (`runAfter` や `type` の兄弟として) 追加できるようになりました。  ワークフローの一環として生成される Azure 診断のテレメトリに含める特定のアクションの入力または出力をこのオブジェクトで指定します。  次に例を示します。

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## <a name="next-steps"></a>次のステップ
* [ロジック アプリのワークフロー定義の使用](../logic-apps/logic-apps-author-definitions.md)
* [ロジック アプリのデプロイ テンプレートの作成](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png



<!--HONumber=Jan17_HO3-->


