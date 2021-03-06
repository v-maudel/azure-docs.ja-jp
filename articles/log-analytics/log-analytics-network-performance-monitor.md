---
title: "OMS のネットワーク パフォーマンス モニター ソリューション | Microsoft Docs"
description: "ネットワーク パフォーマンス モニターは、ネットワークのパフォーマンスを監視し、ネットワーク パフォーマンスのボトルネックをほぼリアルタイムで検出して特定するのに役立ちます。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: d1cae87bb312ef903d099b8be59ad39a5b83d468
ms.openlocfilehash: 4b683ef50ca1046686213b55c32e07b5fb8cca68


---
# <a name="network-performance-monitor-preview-solution-in-oms"></a>OMS のネットワーク パフォーマンス モニター (プレビュー) ソリューション
> [!NOTE]
> これは[プレビュー ソリューション](log-analytics-add-solutions.md#preview-management-solutions-and-features)です。
>
>

このドキュメントでは、OMS のネットワーク パフォーマンス モニター ソリューションを設定して使用する方法を説明します。このソリューションは、ネットワークのパフォーマンスを監視し、ネットワーク パフォーマンスのボトルネックをほぼリアルタイムで検出して特定するのに役立ちます。 ネットワーク パフォーマンス モニター ソリューションでは、2 つのネットワーク、サブネット、またはサーバーの間の損失と待機時間を監視できます。 トラフィックのブラックホール、ルーティング エラーなどのネットワークの問題のほか、従来のネットワーク監視手法では検出されない問題を検出します。 ネットワーク リンクに関するしきい値を超えた場合に、ネットワーク パフォーマンス モニターによってアラートの生成と通知が行われます。 これらのしきい値は、システムに自動的に学習させることができます。または、カスタム アラート ルールを使用するように構成できます。 ネットワーク パフォーマンス モニターを使用すると、ネットワーク パフォーマンスの問題をタイムリーに検出できるほか、問題の原因を特定のネットワーク セグメントまたはデバイスに限定できます。

ソリューション ダッシュボードを使用することで、ネットワークの問題を検出できます。最近のネットワーク正常性イベント、異常なネットワーク リンク、パケット損失が多く待機時間の長いサブネットワーク リンクなど、ネットワークに関する概要がこのダッシュボードに表示されます。 ネットワーク リンクの詳細を表示し、サブネットワーク リンクとノード間リンクの現在の正常性状態を確認できます。 ネットワーク、サブネットワーク、ノード間の各レベルで、損失と待機時間の過去の傾向を表示することもできます。 パケット損失と待機時間に関する過去の傾向グラフを表示して一時的なネットワークの問題を検出できるほか、トポロジ マップでネットワークのボトルネックを特定できます。 対話型のトポロジ グラフを使用すると、ホップバイホップのネットワーク ルートを視覚化して問題の原因を特定できます。 他のソリューションと同様に、ネットワーク パフォーマンス モニターによって収集されたデータに基づいてカスタム レポートを作成するために、さまざまな分析要件にログ検索を使用できます。

このソリューションでは、ネットワーク障害を検出する主要なメカニズムとして代理トランザクションが使用されています。 このため、特定のネットワーク デバイスのベンダーまたはモデルに関係なくソリューションを使用できます。 ソリューションはオンプレミス、クラウド (IaaS)、ハイブリッド環境をまたいで動作し、 ネットワーク トポロジとネットワークのさまざまなルートを自動的に検出します。

一般的なネットワーク監視製品では、ネットワーク デバイス (ルーター、スイッチなど) の正常性の監視が主な機能であり、2 点間のネットワーク接続の実際の品質に関する洞察は得られません。ネットワーク パフォーマンス モニターでは、そのような洞察を得ることができます。

### <a name="using-the-solution-standalone"></a>単独でのソリューションの使用
重要性の極めて高いワークロード、ネットワーク、データセンター、またはオフィス サイト間にあるネットワーク接続の品質を監視する場合、ネットワーク パフォーマンス モニター ソリューションを使って次の接続の正常性を自動で監視できます。

* パブリック ネットワークまたはプライベート ネットワークを使用して接続された、複数のデータセンターまたはオフィス サイト
* 基幹業務アプリケーションが実行されている、重要性の極めて高いワークロード
* Microsoft Azure や Amazon Web Services (AWS) などのパブリック クラウド サービスとオンプレミスのネットワーク (利用できる IaaS (VM) があり、オンプレミス ネットワークとクラウド ネットワークの間で通信できるように構成されたゲートウェイがある場合)
* Azure とオンプレミスのネットワーク (Express Route を使用している場合)

### <a name="using-the-solution-with-other-networking-tools"></a>他のネットワーク ツールとのソリューションの併用
基幹業務アプリケーションを監視する場合、ネットワーク パフォーマンス モニター ソリューションを他のネットワーク ツールに対する併用ソリューションとして使用することができます。 ネットワークの遅延はアプリケーションの低速化を招くおそれがあります。ネットワーク パフォーマンス モニターは、根本にあるネットワークの問題によって引き起されたアプリケーション パフォーマンスの問題を調査するのに役立ちます。 このソリューションではネットワーク デバイスへのアクセスが不要なため、アプリケーション管理者は、ネットワークがアプリケーションに及ぼしている影響に関する情報をネットワーク チームから提供してもらう必要がありません。

さらに、既に他のネットワーク監視ツールを運用している場合、これらのツールをネットワーク パフォーマンス監視ソリューションで補完できます。これは、ほとんどの従来のネットワーク監視ソリューションでは、損失や待機時間などのエンドツーエンドのネットワーク パフォーマンス メトリックに関する洞察を得ることができないためです。  ネットワーク パフォーマンス モニター ソリューションを使用すれば、この点を補うことができます。

## <a name="installing-and-configuring-agents-for-the-solution"></a>ソリューションのエージェントのインストールと構成
「[Windows コンピューターを Log Analytics に接続する](log-analytics-windows-agents.md)」と「[Operations Manager を Log Analytics に接続する](log-analytics-om-agents.md)」にある、エージェントをインストールするための基本的な手順に従ってください。

> [!NOTE]
> ネットワーク リソースを検出して監視するうえで十分なデータを確保するために、少なくとも 2 つのエージェントをインストールする必要があります。 エージェントを&2; つ以上インストールしていないと、ソリューションは追加のエージェントをインストールして構成するまで構成中の状態のままになります。
>
>

### <a name="where-to-install-the-agents"></a>エージェントをインストールする場所
エージェントをインストールする前に、ネットワークのトポロジと、ネットワークの監視したい箇所について検討してください。 監視する各サブネットに&2; つ以上のエージェントをインストールすることをお勧めします。 つまり、監視する各サブネットに関して&2; つ以上のサーバーまたは VM を選択し、それらにエージェントをインストールします。

ネットワークのトポロジがわからない場合、ネットワーク パフォーマンスを監視する重要なワークロードがあるサーバーにエージェントをインストールしてください。 たとえば、SQL Server が実行されているサーバーと Web サーバーの間のネットワーク接続を追跡したいとします。 この場合、両方のサーバーにエージェントをインストールします。

エージェントによって監視されるのはホストそのものではなく、ホスト間のネットワーク接続 (リンク) です。 そのため、ネットワーク リンクを監視するには、このリンクの両方のエンドポイントにエージェントをインストールする必要があります。

### <a name="configure-agents"></a>エージェントの構成
エージェントをインストールした後、これらのコンピューターのファイアウォール ポートを開き、エージェントが通信できるようにする必要があります。 [EnableRules.ps1 PowerShell スクリプト](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634)をダウンロードし、管理者特権の PowerShell ウィンドウでパラメーターを指定せずにそれを実行する必要があります。

スクリプトを実行すると、ネットワーク パフォーマンス モニターに必要なレジストリ キーが作成されるほか、エージェントどうしの TCP 接続を可能にする Windows ファイアウォール規則が作成されます。 スクリプトによって作成されたレジストリ キーは、デバッグ ログとログ ファイルのパスを記録するかどうかも指定します。 また、通信で使用されるエージェント TCP ポートを定義します。 これらのキーの値は、スクリプトによって自動的に設定されるため、これらのキーを手動で変更しないようにしてください。

既定で開かれるポートは 8084 です。 パラメーター `portNumber` をスクリプトに加えることでカスタム ポートを使用できます。 ただし、スクリプトが実行されるすべてのコンピューターで同じポートを使用する必要があります。

> [!NOTE]
> EnableRules.ps1 スクリプトでは、スクリプトが実行されるコンピューターのみに対する Windows ファイアウォール規則が構成されます。 ネットワーク ファイアウォールがある場合、ネットワーク パフォーマンス モニターによって使用されている TCP ポート宛てのトラフィックを必ず許可する必要があります。
>
>

## <a name="configuring-the-solution"></a>ソリューションの構成
次の情報を使用して、ソリューションをインストールおよび構成します。

1. ネットワーク パフォーマンス モニター ソリューションは、Windows Server 2008 SP 1 以降、または Windows 7 SP1 以降が実行されているコンピューターからデータを取得します。このコンピューターの要件は Microsoft Monitoring Agent (MMA) と同じです。
2. 「[ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)」で説明されている手順に従ってネットワーク パフォーマンス モニター ソリューションを OMS ワークスペースに追加します。  
   ![ネットワーク パフォーマンス モニターのシンボル](./media/log-analytics-network-performance-monitor/npm-symbol.png)
3. OMS ポータルで、**[ネットワーク パフォーマンス モニター]** という名前の新しいタイルが *"このソリューションにはさらに構成が必要です"* というメッセージと共に表示されます。 ソリューションを構成し、エージェントによって検出されたサブネットワークとノードに基づいてネットワークを追加する必要があります。 **[ネットワーク パフォーマンス モニター]** をクリックし、既定のネットワークの構成を開始します。  
   ![追加の構成が必要なソリューション](./media/log-analytics-network-performance-monitor/npm-config.png)

### <a name="configure-the-solution-with-a-default-network"></a>既定のネットワークを使用したソリューションの構成
構成ページで、**Default** という名前の単一のネットワークが表示されます。 ネットワークを&1; つも定義していない場合、自動的に検出されたすべてのサブネットは Default ネットワークに配置されます。

ネットワークを作成したら、そこにサブネットを追加します。そうすると、そのサブネットは Default ネットワークから削除されます。 ネットワークを削除すると、その中のすべてのサブネットは Default ネットワークに自動的に戻されます。

つまり Default ネットワークは、ユーザー定義のネットワークに含まれていないすべてのサブネットのためのコンテナーです。 Default ネットワークは編集したり削除したりすることができず、 常にシステム内に存在します。 しかし、ネットワークは必要な数だけ作成できます。

ほとんどの場合、組織のサブネットは&2; つ以上のネットワークに配置されるため、1 つ以上のネットワークを作成してサブネットを論理的にグループ分けする必要があります。

### <a name="create-new-networks"></a>新しいネットワークの作成
ネットワーク パフォーマンス モニターのネットワークは、サブネット用のコンテナーです。 任意の名前を付けたネットワークを作成し、そのネットワークにサブネットを追加できます。 たとえば、*Building1* という名前のネットワークを作成してサブネットを追加できます。または、*DMZ* という名前のネットワークを作成し、非武装地帯に属しているすべてのサブネットをそのネットワークに追加できます。

#### <a name="to-create-a-new-network"></a>新しいネットワークを作成するには
1. **[ネットワークの追加]** をクリックし、ネットワークの名前と説明を入力します。
2. 1 つ以上のサブネットを選択し、**[追加]** をクリックします。
3. **[保存]** をクリックして構成を保存します。  
   ![ネットワークの追加](./media/log-analytics-network-performance-monitor/npm-add-network.png)

### <a name="wait-for-data-aggregation"></a>データ集計の待機
初めて構成を保存した後、エージェントがインストールされたノードの間で発生したネットワーク パケット損失と待機時間に関する情報の収集が、ソリューションによって開始されます。 このプロセスにはしばらく時間がかかることがあります。ときには、30 分を超える場合もあります。 この状態の間、概要ページの [ネットワーク パフォーマンス モニター] タイルには、*"Data aggregation in process (データ集計が進行中)"* というメッセージが表示されます。

![進行中のデータ集計](./media/log-analytics-network-performance-monitor/npm-aggregation.png)

データがアップロードされると、更新された [ネットワーク パフォーマンス モニター] タイルが表示され、データを確認できるようになります。

![[ネットワーク パフォーマンス モニター] タイル](./media/log-analytics-network-performance-monitor/npm-tile.png)

タイルをクリックして [ネットワーク パフォーマンス モニター] ダッシュボードを表示します。

![ネットワーク パフォーマンス モニターのダッシュボード](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="edit-monitoring-settings-for-subnets"></a>サブネットの監視設定の編集
少なくとも&1; つのエージェントがインストールされているサブネットはすべて、構成ページの **[サブネットワーク]** タブに一覧表示されます。

#### <a name="to-enable-or-disable-monitoring-for-particular-subnetworks"></a>特定のサブネットワークの監視を有効または無効にするには
1. **[サブネットワーク ID]** の横にあるボックスをオンまたはオフにします。必要に応じて、**[監視に使用します]** ボックスをオンまたはオフにしてください。 複数のサブネットをオンまたはオフにできます。 無効にすると、他のエージェントへの ping の実行を停止するようにエージェントが更新されるため、サブネットワークが監視されません。
2. 特定のサブネットワークについて監視するノードを選択します。このとき、一覧からサブネットワークを選択し、監視しないノードの一覧と監視するノードの一覧の間で必要なノードを移動します。
   必要に応じて、カスタムの**説明**をサブネットワークに追加できます。
3. **[保存]** をクリックして構成を保存します。  
   ![サブネットの編集](./media/log-analytics-network-performance-monitor/npm-edit-subnet.png)

### <a name="choose-nodes-to-monitor"></a>監視するノードの選択
エージェントがインストールされているすべてのノードは、**[ノード]** タブに一覧表示されます。

#### <a name="to-enable-or-disable-monitoring-for-nodes"></a>ノードの監視を有効または無効にするには
1. 監視するノードを選択します。または、監視を停止するノードの選択を解除します。
2. 必要に応じて、**[監視に使用します]** ボックスをオンまたはオフにします。
3. [ **Save**] をクリックします。  
   ![ノードの監視の有効化](./media/log-analytics-network-performance-monitor/npm-enable-node-monitor.png)

### <a name="set-monitoring-rules"></a>監視ルールの設定
ネットワーク パフォーマンス モニターは、しきい値を超えた場合に、一対のノードの間の接続、あるいは一対のサブネットワーク リンクまたはネットワーク リンクの間の接続に関する正常性イベントを生成します。 これらのしきい値は、システムに自動的に学習させることができます。または、カスタム アラート ルールを使用するように構成できます。

"*既定のルール*" はシステムによって作成されます。一対のネットワークまたはサブネットワーク リンクの間で発生した損失または待機時間がシステムの学習したしきい値を超えた場合に、このルールに従って正常性イベントが作成されます。 既定のルールを無効にしてカスタム監視ルールを作成することもできます。

#### <a name="to-create-custom-monitoring-rules"></a>カスタム監視ルールを作成するには
1. **[モニター]** タブの **[ルールの追加]** をクリックし、ルールの名前と説明を入力します。
2. 監視する一対のネットワーク リンクまたはサブネットワーク リンクを一覧から選択します。
3. まず、対象となる最初のサブネットワークが存在するネットワークをネットワーク ドロップダウンから選択します。その後、対応するサブネットワーク ドロップダウンからサブネットワークを選択します。
   ネットワーク リンク内のすべてのサブネットワークを監視する場合、**[すべてのサブネットワーク]** を選択します。 同様に、対象となるもう&1; つのサブネットワークを選択します。 **[例外を追加]** をクリックすると、選択したサブネットワーク リンク中の特定のものを監視の対象から除外できます。
4. 選択した項目の正常性イベントを作成したくない場合、**[このルールの対象になるリンクの稼働状況の監視を有効にする]** をオフにします。
5. 監視条件を選択します。
   しきい値を入力して、正常性イベントの生成に関するカスタムしきい値を設定できます。 選択したネットワーク ペア/サブネットワーク ペアに対して選択したしきい値を条件の値が上回ると、正常性イベントが生成されます。
6. **[保存]** をクリックして構成を保存します。  
   ![カスタム監視ルールの作成](./media/log-analytics-network-performance-monitor/npm-monitor-rule.png)

### <a name="choose-the-right-protocol-icmp-or-tcp"></a>適切なプロトコルの選択 - ICMP または TCP

ネットワーク パフォーマンス モニター (NPM) では、代理トランザクションを使用して、パケット損失やリンク待ち時間などのネットワーク パフォーマンス メトリックスを計算します。 この理解を深めるために、ネットワーク リンクの一方の終端に接続された NPM エージェントについて考えてみましょう。 この NPM エージェントは、ネットワークのもう一方の終端に接続された&2; つ目の NPM エージェントにプローブ パケットを送信します。 2 つ目のエージェントは応答パケットで応答します。 このプロセスが数回繰り返されます。 1 つ目の NPM エージェントは、応答の数と各応答を受信するまでの所要時間を測定することで、リンク待ち時間とパケットの破棄を評価します。

これらのパケットの形式、サイズ、シーケンスは、監視ルールの作成時に選択したプロトコルによって決定されます。 パケットのプロトコルに基づいて、中間ネットワーク デバイス (ルーター、スイッチなど) はこれらのパケットを異なる方法で処理することがあります。 そのため、プロトコルの選択は結果の精度に影響します。 また、プロトコルの選択によって、NPM ソリューションのデプロイ後に手動の手順を実行する必要があるかどうかも決まります。

NPM では、代理トランザクションを実行するために、ICMP と TCP のいずれかのプロトコルを選択できます。
代理トランザクションのルールを作成するときに ICMP を選択した場合、NPM エージェントは ICMP ECHO メッセージを使用してネットワーク待ち時間とパケット損失を計算します。 ICMP ECHO では、従来の Ping ユーティリティによって送信されるものと同じメッセージを使用します。 プロトコルとして TCP を使用した場合、NPM エージェントはネットワーク経由で TCP SYN パケットを送信します。 その後、TCP ハンドシェイクが完了すると、RST パケットを使用して接続が削除されます。

#### <a name="points-to-consider-before-choosing-the-protocol"></a>プロトコルを選択する前に考慮すべき点
使用するプロトコルを選択する前に、次の情報を考慮してください。

##### <a name="discovering-multiple-network-routes"></a>複数のネットワーク ルートの検出
TCP では、複数のルートを検出する際の精度が高く、各サブネットに必要なエージェントの数も少なくなります。 たとえば、TCP を使用する&1; つまたは&2; つのエージェントでサブネット間のすべての冗長パスを検出できます。 しかし、ICMP を使用して同様の結果を得るには、複数のエージェントが必要になります。 ICMP を使用する場合、2 つのサブネット間に *N* 個のルートがあるとすると、送信元サブネットまたは宛先サブネットに 5*N* 個以上のエージェントが必要になります。

##### <a name="accuracy-of-results"></a>結果の精度
ルーターやスイッチは、ICMP ECHO パケットに対して TCP パケットよりも低い優先順位を割り当てる傾向があります。 特定の状況で、ネットワーク デバイスの負荷が大きいときには、TCP で取得されるデータはアプリケーションで発生する損失と待ち時間をより厳密に反映します。 これは、アプリケーション トラフィックのほとんどが TCP 経由で流れるためです。 このような場合、TCP に比べ、ICMP では結果の精度が低くなります。

##### <a name="firewall-configuration"></a>ファイアウォールの構成
TCP プロトコルでは、TCP パケットが宛先ポートに送信される必要があります。 NPM エージェントで使用される既定のポートは 8084 ですが、エージェントの構成時にこれを変更できます。 そのため、ネットワーク ファイアウォールまたは (Azure での) NSG 規則が、このポートでのトラフィックを許可していることを確認する必要があります。 また、エージェントがインストールされているコンピューターのローカル ファイアウォールが、このポートでのトラフィックを許可するように構成されていることも確認する必要があります。

Windows を実行しているコンピューターのファイアウォール規則は PowerShell スクリプトを使用して構成できますが、ネットワーク ファイアウォールは手動で構成する必要があります。

これに対して、ICMP はポートを使用せずに動作します。 ほとんどのエンタープライズ シナリオでは、Ping ユーティリティなどのネットワーク診断ツールを使用できるように、ICMP トラフィックはファイアウォールで許可されています。 そのため、コンピューター間で ping を実行できれば、ファイアウォールを手動で構成しなくても、ICMP プロトコルを使用できます。

> [!NOTE]
> 使用すべきプロトコルがわからない場合は、ICMP を最初に選択します。 満足のいく結果が得られない場合、後でいつでも TCP に切り替えることができます。


#### <a name="how-to-switch-the-protocol"></a>プロトコルを切り替える方法

デプロイ時に ICMP を使用することを選択した場合、既定の監視ルールを編集することで、いつでも TCP に切り替えることができます。

##### <a name="to-edit-the-default-monitoring-rule"></a>既定の監視ルールを編集するには
1.  **[ネットワーク パフォーマンス]** > **[モニター]** > **[構成]** > **[モニター]** に移動し、**[既定のルール]** をクリックします。
2.  **[プロトコル]** セクションまでスクロールし、使用するプロトコルを選択します。
3.  **[保存]** をクリックして変更を適用します。

既定のルールで特定のプロトコルを使用している場合でも、別のプロトコルを使用する新しいルールを作成できます。 一部のルールで ICMP を使用し、別のルールで TCP を使用している場合は、ルールの組み合わせを作成することもできます。






## <a name="data-collection-details"></a>データ収集の詳細
ネットワーク パフォーマンス モニターでは、損失と待機時間についての情報を収集するために TCP SYN-SYNACK-ACK ハンドシェイク パケットが使用されます。また、トポロジ情報の取得に traceroute が使用されます。

次の表は、ネットワーク パフォーマンス モニターにおけるデータの収集に関して、その手段と各種情報をまとめたものです。

| プラットフォーム | 直接エージェント | SCOM エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![あり](./media/log-analytics-network-performance-monitor/oms-bullet-green.png) |![あり](./media/log-analytics-network-performance-monitor/oms-bullet-green.png) |![なし](./media/log-analytics-network-performance-monitor/oms-bullet-red.png) |![いいえ](./media/log-analytics-network-performance-monitor/oms-bullet-red.png) |![いいえ](./media/log-analytics-network-performance-monitor/oms-bullet-red.png) |TCP ハンドシェイク (5 秒ごと)、データ送信 (3 分ごと) |

このソリューションでは、ネットワークの正常性の評価に代理トランザクションが使用されます。 ネットワークの各所にインストールされた OMS エージェントは相互に TCP パケットを交換し、このプロセスの中でラウンドトリップ時間を取得します。パケット損失がある場合はその情報も取得します。 各エージェントはさらに、他のエージェントに対して traceroute を定期的に実行し、テストが必要なネットワークのさまざまなルートをすべて検出します。 このデータを使用することで、エージェントはネットワーク待機時間とパケット損失の数値を推定できます。 テストは&5; 秒おきに繰り返され、データはエージェントによって&3; 分間集計されて OMS にアップロードされます。

> [!NOTE]
> エージェントは互いに頻繁に通信しますが、テストの実行中に大量のネットワーク トラフィックを生成することはありません。 損失と待機時間を特定するうえでエージェントが使用するのは TCP SYN-SYNACK-ACK ハンドシェイク パケットのみであり、データ パケットが交換されることはありません。 このプロセスの間、エージェントは必要な場合にのみ互いに通信します。また、エージェント通信トポロジは、ネットワーク トラフィックを抑制するように最適化されます。
>
>

## <a name="using-the-solution"></a>ソリューションの使用
このセクションでは、すべてのダッシュボード機能とそれらの使用方法について説明します。

### <a name="solution-overview-tile"></a>ソリューションの概要タイル
ネットワーク パフォーマンス モニター ソリューションを有効にすると、OMS の [概要] ページのソリューション タイルでネットワークの正常性の簡単な概要を確認できます。 このタイルには、正常なサブネットワーク リンクと異常なサブネットワーク リンクの数を示すドーナツ グラフが表示されます。 タイルをクリックすると、ソリューション ダッシュボードが開きます。

![[ネットワーク パフォーマンス モニター] タイル](./media/log-analytics-network-performance-monitor/npm-tile.png)

### <a name="network-performance-monitor-solution-dashboard"></a>ネットワーク パフォーマンス モニター ソリューションのダッシュボード
**[ネットワークの概要]** ブレードには、ネットワークとそれに関連したサイズの概要が表示されます。 その次に、システム中のネットワーク リンク、サブネット リンク、パスの総数を示すタイルが続きます (パスは、エージェントがある&2; つのホストとそれらの間に存在するすべてのホップの IP アドレスで構成されます)。

**[上位ネットワーク正常性イベント]** ブレードには、システムの最近の正常性イベントとアラートのほか、イベントがアクティブになってから経過した時間の一覧が表示されます。 正常性イベントまたはアラートは、ネットワークまたはサブネットワークのリンクのパケット損失または待機時間がしきい値を超えた場合に生成されます。

**[上位異常ネットワーク リンク]** ブレードには、異常なネットワーク リンクの一覧が表示されます。 これらのネットワーク リンクには現在、1 つ以上の好ましくない正常性イベントがあります。

**[Top Subnetwork Links with Most Loss (損失が最も大きい上位サブネットワーク リンク)]** ブレードと **[待機時間が最も長いサブネットワーク リンク]** ブレードにはそれぞれ、パケット損失の多い上位サブネットワーク リンクと待機時間の長い上位サブネットワーク リンクが表示されます。 特定のネットワーク リンクでは、待機時間が非常に長くなったり、パケット損失の量が多くなったりすることが予想されます。 このようなリンクは、上位&10; 個のリストに表示されますが、異常としてはマークされません。

**[共通クエリ]** ブレードには、未加工のネットワーク監視データを直接フェッチする検索クエリのセットが表示されます。 これらのクエリは、カスタム レポート用の独自のクエリを作成するための出発点として使用できます。

![ネットワーク パフォーマンス モニターのダッシュボード](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="drill-down-for-depth"></a>詳細のドリルダウン
ソリューション ダッシュボードのさまざまなリンクをクリックし、興味のある点の詳細を確認することができます。 たとえば、ダッシュボードに表示されるアラートまたは異常なネットワーク リンクを確認する場合は、それをクリックして詳しく調査できます。 特定のネットワーク リンクのサブネットワーク リンクがすべて一覧表示されたページに移動します。 各サブネットワーク リンクの損失、待機時間、正常性状態を確認し、問題を引き起こしているサブネットワーク リンクをすばやく特定できるようになります。 さらに **[ノード リンクの表示]** をクリックすると、異常なサブネット リンクのノード リンクをすべて表示できます。 その後、個別のノード間リンクを確認し、異常なノード リンクを見つけることができます。

**[トポロジの表示]** をクリックすると、ソース ノードと宛先ノードの間に存在するルートのホップバイホップ トポロジを表示できます。 異常なルートまたはホップが赤色で表示されるため、ネットワークの特定の部分に問題をすばやく特定できます。

![データのドリルダウン](./media/log-analytics-network-performance-monitor/npm-drill.png)

#### <a name="trend-charts"></a>傾向グラフ
ドリルダウンの各レベルでは、ネットワーク リンクの損失と待機時間に関する傾向を確認できます。 傾向グラフは、サブネットワーク リンクとノード リンクでも使用できます。 グラフの上部にある時間コントロールを使用することで、グラフをプロットする時間間隔を変更できます。

傾向グラフには、ネットワーク リンクのパフォーマンスの履歴が表示されます。 ネットワークの問題には、一過性の性質があり、ネットワークの現在の状態を確認するだけでは把握が困難なものもあります。 これは、問題が急に現れてだれかに気付かれる前に消え、時間が経ってからでないと再び現れない場合があるためです。 このような一時的な問題は、アプリケーション管理者にとっても困難なものである場合があります。なぜなら、これらの問題は、すべてのアプリケーション コンポーネントがスムーズに実行されているように見える場合であっても、アプリケーションの応答時間の説明できない増加として現れることが多いためです。

ネットワーク待機時間またはパケット損失の急な増加から問題が見て取れる傾向グラフを確認することで、これらの種類の問題を簡単に検出できます。

![傾向グラフ](./media/log-analytics-network-performance-monitor/npm-trend.png)

#### <a name="hop-by-hop-topology-map"></a>ホップバイホップ トポロジ マップ
ネットワーク パフォーマンス モニターでは、2 つのノードの間に存在するホップバイホップ トポロジが対話型のトポロジ マップで表示されます。 トポロジ マップは、ノード リンクを選択して **[トポロジの表示]** をクリックすることで表示できます。 また、ダッシュボードの **[パス]** タイルをクリックして表示することもできます。 ダッシュボードの **[パス]** をクリックしたら、左側のパネルからソース ノードと宛先ノードを選択してから、**[プロット]** をクリックして&2; つのノードの間にあるルートをプロットする必要があります。

トポロジ マップでは、2 つのノードの間に存在するルートの数のほか、データ パケットに使用されたパスが表示されます。 ネットワーク パフォーマンスのボトルネックはトポロジ マップで赤色でマークされます。 トポロジ マップの赤色の要素を確認して、問題のあるネットワーク接続またはネットワーク デバイスの場所を特定できます。

ノードをクリックするか、トポロジ マップのノードにポインターを置くと、FQDN や IP アドレスなど、ノードのプロパティが表示されます。 ホップをクリックすると、その IP アドレスが表示されます。 特定のルートを強調表示するには、消去してからマップで強調表示するルートのみを選択します。 トポロジ マップはマウス ホイールを使って拡大したり縮小したりできます。

次のマップに示すトポロジはレイヤー 3 トポロジであり、レイヤー 2 のデバイスと接続は含まれていないことに注意してください。

![ホップバイホップ トポロジ マップ](./media/log-analytics-network-performance-monitor/npm-topology.png)

#### <a name="fault-localization"></a>障害の特定
ネットワーク パフォーマンス モニターでは、ネットワーク デバイスに接続することなくネットワークのボトルネックを見つけることができます。 ネットワークからデータが収集され、それに基づいてネットワーク グラフに高度なアルゴリズムが適用されます。これにより、問題の原因である確率が高いネットワークの部分がネットワーク パフォーマンス モニターによって確率論的に推定されます。

この手法は、ホップへのアクセスが利用できない場合にネットワークのボトルネックを特定するのに役立ちます。これは、ルーターやスイッチなどのネットワーク デバイスからデータを収集する必要がないためです。 この手法はさらに、2 つのノードの間にあるホップが管理制御下にない場合にも役立ちます。 たとえば、ホップが ISP ルーターである場合などです。

### <a name="log-analytics-search"></a>Log Analytics 検索
すべてのデータは、ネットワーク パフォーマンス モニター ダッシュボードで視覚的に表示されます。さらに、ドリルダウン ページは Log Analytics 検索でもネイティブに使用できます。 検索クエリ言語を使用してデータを照会することができるほか、データを Excel または PowerBI にエクスポートしてカスタム レポートを作成できます。 ダッシュボードの **[共通クエリ]** ブレードには、便利なクエリがいくつかあります。これらのクエリは、独自のクエリとレポートを作成するための出発点として利用できます。

![検索クエリ](./media/log-analytics-network-performance-monitor/npm-queries.png)

## <a name="investigate-the-root-cause-of-a-health-alert"></a>正常性アラートの根本原因の調査
ネットワーク パフォーマンス モニターについてひととおり確認したところで、正常性イベントの根本原因の簡単な調査について見ていきます。

1. [概要] ページで **[ネットワーク パフォーマンス モニター]** タイルに注目して、ネットワークの正常性の簡単なスナップショットを確認します。 監視されている 80 個のサブネットワーク リンクのうち、43 個が正常でないことがわかります。 これは調査が必要です。 タイルをクリックしてソリューション ダッシュボードを表示します。  
   ![ネットワーク パフォーマンス モニターのタイル](./media/log-analytics-network-performance-monitor/npm-investigation01.png)
2. 下に例示されている図から、現時点で 4 つの正常性イベントがあり、4 つの異常なネットワーク リンクが存在することがわかります。 問題を調査することに決め、**[Sharepoint-Web (Sharepoint から Web)]** のネットワーク リンクをクリックして問題の根本原因を確認します。  
   ![異常なネットワーク リンクの例](./media/log-analytics-network-performance-monitor/npm-investigation02.png)
3. ドリルダウン ページには、**[Sharepoint-Web (Sharepoint から Web)]** のネットワーク リンクのすべてのサブネットワーク リンクが表示されます。 2 つのサブネットワーク リンクの待機時間がしきい値を超えており、そのためネットワーク リンクの状態が異常になっていることがわかります。 両方のサブネットワーク リンクについて待機時間の傾向を確認することもできます。 また、グラフの時間の選択コントロールを使用し、必要な時間範囲に絞り込むことができます。 さらに、待機時間がピークに到達した時刻を確認できるほか、 後でこの期間のログを検索して問題を調査することができます。 詳しく調査するには、**[ノード リンクの表示]** をクリックします。  
   ![異常なサブネット リンクの例](./media/log-analytics-network-performance-monitor/npm-investigation03.png)
4. 前のページと同様に、特定のサブネットワーク リンクのドリルダウン ページで、それを構成するノード リンクが一覧表示されます。 前の手順で実行したのと同様のアクションをここでも実行できます。 **[トポロジの表示]** をクリックして 2 つのノードの間にあるトポロジを表示します。  
   ![異常なノード リンクの例](./media/log-analytics-network-performance-monitor/npm-investigation04.png)
5. 選択した 2 つのノードの間に存在するパスはすべてトポロジ マップにプロットされます。 2 つのノードの間にあるルートのホップバイホップ トポロジを、トポロジ マップで視覚化できます。 これにより、2 つのノードの間に存在するルートの数とデータ パケットに使用されたパスが、わかりやすい図で表示されます。 ネットワーク パフォーマンスのボトルネックは赤色でマークされています。 トポロジ マップの赤色の要素を確認して、問題のあるネットワーク接続またはネットワーク デバイスの場所を特定できます。  
   ![異常なトポロジの表示の例](./media/log-analytics-network-performance-monitor/npm-investigation05.png)
6. 各パスの損失、待機時間、ホップの数は、**[パスの詳細]** ウィンドウで確認できます。 ウィンドウに示されているように、この例では 3 つの異常なパスがあることがわかります。 スクロール バーを使ってこれらの異常なパスの詳細を表示します。  1 つのパスのみについてトポロジがプロットされるように、チェック ボックスを使用していずれかのパスを選択します。 トポロジ マップはマウス ホイールを使って拡大したり縮小したりできます。

   次の画像では、赤色のパスとホップを確認することで、問題点の根本原因がネットワークの特定のセクションにあることがはっきりとわかります。 トポロジ マップのノードをクリックすると、FQDN や IP アドレスなど、ノードのプロパティが表示されます。 また、ホップをクリックするとその IP アドレスが表示されます。  
   ![異常なトポロジ - パスの詳細の例](./media/log-analytics-network-performance-monitor/npm-investigation06.png)

## <a name="next-steps"></a>次のステップ
* 詳細なネットワーク パフォーマンスのデータ レコードを表示するために、[ログを検索](log-analytics-log-searches.md)します。



<!--HONumber=Feb17_HO1-->


