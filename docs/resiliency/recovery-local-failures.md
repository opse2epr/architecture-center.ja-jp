---
title: "技術ガイダンス: Azure でのローカル障害からの復旧"
description: "回復力のあるアプリケーション、高可用性のアプリケーション、およびフォールト トレラント アプリケーションの内容および設計方法、ならびに Azure 内でのローカル障害を対象にしたディザスター リカバリーの計画策定に関する記事。"
author: adamglick
ms.date: 08/18/2016
ms.openlocfilehash: 180eb465e5f82406bb03924a29d5b06d43bbaa24
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2017
---
[!INCLUDE [header](../_includes/header.md)]
# <a name="azure-resiliency-technical-guidance-recovery-from-local-failures-in-azure"></a>Azure の回復性に関する技術ガイダンス - Azure でのローカル障害からの復旧

アプリケーションの可用性にとって脅威となるのは、主に次の 2 つです。

* ドライブやサーバーなどのデバイスの障害
* ピーク時の負荷条件下でのコンピューティングなどの重大なリソースの枯渇

Azure には、このような状況で高可用性を有効にする、リソース管理、柔軟性、負荷分散、およびパーティション分割の組み合わせが用意されています。 これらの機能の一部は、すべての Azure サービスに対して、自動的に実行されます。 ただし、場合によっては、アプリケーション開発者が効果を上げるために追加の作業を行う必要があります。

## <a name="cloud-services"></a>Cloud Services
Azure Cloud Services は、1 つ以上の Web ロールまたは worker ロールの集合で構成されています。 ロールの 1 つ以上のインスタンスは同時に実行できます。 インスタンスの数は、構成によって決まります。 ロール インスタンスは、ファブリック コントローラーと呼ばれるコンポーネントによって監視および管理されます。 ファブリック コントローラーは、ソフトウェアとハードウェア両方の障害を自動的に検出し、対応します。

すべてのロール インスタンスは独自の仮想マシン (VM) で実行され、ゲスト エージェントを通じてそのファブリック コントローラーと通信します。 ゲスト エージェントは、リソースおよびノードのメトリックを収集します。これには、VM の使用状況、状態、ログ、リソースの使用状況、例外、および障害の状態が含まれます。 ファブリック コントローラーは、構成可能な間隔でゲスト エージェントを照会し、ゲスト エージェントが応答に失敗した場合に VM を再起動します。 ハードウェアで障害が発生した場合、関連付けられているファブリック コントローラーは影響を受けるすべてのロール インスタンスを新しいハードウェア ノードに移動し、そこでトラフィックをルーティングするようにネットワークを再構成します。

これらの機能の恩恵を受けるには、開発者はすべてのサービス ロールがロール インスタンスに状態を保存しないようにする必要があります。 代わりに、すべての持続データには、Azure Storage や Azure SQL Database などの永続的なストレージからアクセスする必要があります。 これにより、すべてのロールが要求を処理できます。 これはまた、ロール インスタンスは一時的または永続的なサービス状態において一貫性を損なうことなくいつでも停止することができることを意味します。

状態を外部のロールに保存する要件には、いくつかの意味合いがあります。 たとえば、Azure Storage テーブルに関連するすべての変更は、可能であれば、単一のエンティティ グループ トランザクションで変更される必要があることを意味します。 もちろん、すべての変更を単一のトランザクションで常に行うことができるわけではありません。 サービスの永続的な状態に対する 2 つ以上の更新にわたる長時間実行操作がロール インスタンスによって中断される場合、ロール インスタンス エラーによって問題が発生しないように特別な注意が必要です。 別のロールがそのような操作を再試行しようとするときは、作業が部分的に完了している場合を予測して処理する必要があります。

たとえば、複数のストア間でデータをパーティション分割するサービスがあるとします。 シャードを再配置している間に worker ロールが停止すると、シャードの再配置が完了しない場合があります。 また、別の worker ロールによって再配置が最初から繰り返された結果、データが孤立したり破損したりする場合もあります。 問題を回避するには、長時間実行操作を次のようにする必要があります。

* *べき等*: 副次的な影響が生じることなく繰り返しが可能であること。 長時間実行操作がべき等になるには、何度実行しても、たとえ実行の途中で中断されても、同じ結果が得られる必要があります。
* *増分再開可能*: 最新の障害の時点から継続できること。 増分再開可能にするために、長時間実行操作は一連のより小さなアトミック操作で構成される必要があります。 また、後続の各呼び出しが先行呼び出しの停止位置を取得できるように、進行状況を永続的ストレージに記録する必要もあります。

最後に、すべての長時間実行操作は成功するまで繰り返し呼び出される必要があります。 たとえば、プロビジョニング操作は Azure キューに配置されて、成功した場合にのみ worker ロールによってキューから削除されます。 中断された操作によって作成されたデータをクリーンアップするために、ガベージ コレクションが必要となる場合があります。

### <a name="elasticity"></a>弾力性
ロールごとに実行されるインスタンスの初期の個数は、各ロールの構成によって決まります。 管理者は、予測される負荷に基づいて、複数のインスタンスで実行されるように各ロールを最初に構成する必要があります。 ただし、ロール インスタンスは使用パターンの変更に合わせて簡単にスケールアップまたはスケールダウンすることができます。 この操作は、Azure ポータルを使用して手動で行うことも、Windows PowerShell、Service Management API、またはサード パーティ製ツールを使用してプロセスを自動化することもできます。 詳細については、 [アプリケーションの自動スケールの方法](/azure/cloud-services/cloud-services-how-to-scale/)に関する記事を参照してください。

### <a name="partitioning"></a>パーティション分割
Azure ファブリック コントローラーは、次の 2 種類のパーティションを使用します。

* *更新ドメイン* は、グループ内でサービスのロール インスタンスをアップグレードするために使用されます。 Azure はサービス インスタンスを複数の更新ドメインにデプロイします。 インプレース更新では、ファブリック コントローラーは 1 つの更新ドメインのすべてのインスタンスを停止して、それらをアップグレードし、再起動してから次の更新ドメインに移動します。 この方法は、更新プロセス中にサービス全体が使用できなくなることを防ぎます。
* *障害ドメイン* では、ハードウェア障害またはネットワーク障害が発生する可能性があるポイントを定義します。 複数のインスタンスがある各ロールに対して、ファブリック コントローラーは局所的なハードウェア障害によってサービスが中断されることを防ぐために、インスタンスが複数の障害ドメインに分散されるようにします。 障害ドメインは、サーバーおよびクラスターの障害の影響を受けるすべてのものを管理します。

[Azure サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/) は、複数の Web ロール インスタンスがそれぞれ異なる障害ドメインやアップグレード ドメインにデプロイされる場合、99.95% 以上の時間で外部接続されることを保証します。 更新ドメインとは異なり、障害ドメインの数を制御する方法はありません。 Azure が自動的に障害ドメインを割り当て、それらにロール インスタンスを分散します。 すべてのロールの少なくとも最初の 2 つのインスタンスは、それぞれ異なる障害ドメインおよびアップグレード ドメインに配置されます。これは少なくとも 2 つのインスタンスがあるすべてのロールが SLA を満たすようにするためです。 それを示したのが次の図です。

![Simplified view of fault domain isolation](./images/technical-guidance-recovery-local-failures/partitioning-1.png)

### <a name="load-balancing"></a>負荷分散
Web ロールへのすべてのインバウンド トラフィックはステートレスな Load Balancer を通過し、その Load Balancer はクライアント要求をロール インスタンス間に分散します。 個々のロール インスタンスにはパブリック IP アドレスがなく、インターネットから直接のアドレス指定することはできません。 Web ロールはステートレスであるため、任意のクライアント要求は任意のロール インスタンスにルーティングできます。 [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck.aspx) イベントは 15 秒ごとに発生します。 これを、ロールがトラフィックを受信する準備ができているかどうか、またはビジー状態であるためにロード バランサーのローテーションから外す必要があるかどうかを示すために使用できます。

## <a name="virtual-machines"></a>Virtual Machines
Azure Virtual Machines は、高可用性に関連するいくつかの点でサービスとしてのプラットフォーム (PaaS) のコンピューティング ロールとは異なります。 場合によっては、高可用性を確保するために追加作業を行う必要があります。

### <a name="disk-durability"></a>ディスクの持続性
PaaS のロール インスタンスとは異なり、仮想マシンのドライブに保存されているデータは仮想マシンが再配置される場合でも永続的です。 Azure 仮想マシンは、Azure Storage に BLOB として存在する VM ディスクを使用します。 Azure Storage の可用性という特性のために、仮想マシンのドライブに保存されているデータも高い可用性を備えています。

ただし、(Windows VM 内の) D ドライブはこの規則の例外です。 D ドライブは VM をホストするラック サーバーの実際の物理記憶領域で、そのデータは VM がリサイクルされると失われます。 D ドライブは一時的な格納を目的としています。 Linux では "通常" (常にというわけではありません)、ローカルの一時ディスクが /dev/sdb ブロック デバイスとして公開されます。 Azure Linux エージェントで /mnt/resource または /mnt マウント ポイント (/etc/waagent.conf で構成可能) としてマウントされるのが一般的です。

### <a name="partitioning"></a>パーティション分割
Azure は PaaS アプリケーションの層 (Web ロールと worker ロール) をネイティブで認識するため、障害ドメインと更新ドメインにそれらを適切に分散できます。 これに対し、サービスとしてのインフラストラクチャ (IaaS) アプリケーションの層は、可用性セットを使用して手動で定義する必要があります。 可用性セットは、IaaS の SLA で必要です。

![Availability sets for Azure virtual machines](./images/technical-guidance-recovery-local-failures/partitioning-2.png)

上の図で、インターネット インフォメーション サービス (IIS) 層 (Web アプリ層として機能) および SQL の層 (データ層として機能) は異なる可用性セットに割り当てられています。 こうすることで、各層のすべてのインスタンスが仮想マシンを障害ドメインに分散することによってハードウェア冗長性を備え、更新中に層全体がダウンしないようになります。

### <a name="load-balancing"></a>負荷分散
VM のトラフィックが VM 全体で分散される必要がある場合は、アプリケーションの VM をグループ化し、特定の TCP または UDP エンドポイントで負荷分散する必要があります。 詳細については、 [仮想マシンの負荷分散](/azure/virtual-machines/virtual-machines-linux-load-balance/?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください。 VM が別のソースからの入力 (キュー メカニズムなど) を受信する場合、Load Balancer は必要ではありません。 Load Balancer は、トラフィックをノードに送信する必要があるかどうかを判断するために基本的な正常性チェックを使用します。 また、VM がトラフィックを受信する必要があるかどうかを判断するために、アプリケーション固有の正常性メトリックを実装する独自のプローブを作成することもできます。

## <a name="storage"></a>Storage
Azure Storage は、Azure の基本の永続的データ サービスです。 BLOB、テーブル、キュー、および VM ディスク ストレージを提供します。 このサービスは、レプリケーションとリソース管理の組み合わせを使用して、単一のデータセンター内で高可用性を提供します。 Azure Storage の可用性についての SLA では、99.9% 以上の時間で、

* データの追加、更新、読み取り、および削除に対する正しい形式の要求が正常に問題なく処理されることが保証されます。
* ストレージ アカウントは、インターネット ゲートウェイに接続できます。

### <a name="replication"></a>レプリケーション
Azure Storage のデータの持続性は、リージョン内の完全に独立した物理ストレージ サブシステムに分散している異なるドライブに、すべてのデータの複数のコピーを維持することによって促進されます。 データは同期的にレプリケートされ、すべてのコピーは書き込みが承認される前にコミットされます。 Azure Storage には強い一貫性があります。これは読み取りが最新の書き込みを反映していることが保証されていることを意味します。 また、データのコピーは、ビット崩壊を検出して修復するために継続的にスキャンされます。ビット崩壊は、格納されているデータの整合性を脅かす脅威ですが、よく見過ごされます。

サービスは Azure Storage を使用するだけで、レプリケーションから恩恵を受けられます。 サービス開発者は、ローカル障害から復旧するために追加の作業を行う必要はありません。

### <a name="resource-management"></a>リソース管理
2014 年 5 月より後に作成されたストレージ アカウントは 500 TB まで拡張できます (以前の最大値は 200 TB)。 追加領域が必要となった場合は、アプリケーションが複数のストレージ アカウントを使用できるように設計する必要があります。

### <a name="virtual-machine-disks"></a>仮想マシンのディスク
仮想マシンのディスクは、Azure Storage にページ BLOB として格納され、Blob Storage とまったく同じ持続性とスケーラビリティのプロパティを提供します。 この設計によって、VM を実行しているサーバーで障害が発生し、VM を別のサーバーで再起動しなければならない場合でも、仮想マシンのディスク上のデータは永続的なものになります。

## <a name="database"></a>データベース
### <a name="sql-database"></a>SQL Database
Azure SQL Database は、データベースをサービスとして提供します。 これにより、リレーショナル データベースに対するプロビジョニング、データの挿入、およびクエリ実行をアプリケーションで迅速に行うことができます。 使い慣れた SQL Server の機能の多くが提供される一方で、ハードウェア、構成、修正、および回復力に関する負担は取り除かれます。

> [!NOTE]
> Azure SQL Database は、SQL Server と 1 対 1 で対応する機能を提供するものではありません。 各クラウド アプリケーションに固有のそれぞれ異なる要件セット (柔軟な拡張、メンテナンス コストを削減するサービスとしてのデータベースなど) を満たすことを目的としています。 詳細については、「 [クラウド SQL Server オプションの選択: Azure SQL (PaaS) Database または Azure VM (IaaS) の SQL Server](/azure/sql-database/sql-database-paas-vs-sql-server-iaas/)」を参照してください。
> 
> 

#### <a name="replication"></a>レプリケーション
Azure SQL Database にはノード レベルのエラーに対する回復力が組み込まれています。 データベースに対するすべての書き込みは、クォーラム コミットの手法を使用して複数のバックグラウンド ノードに自動的にレプリケートされます。 トランザクションが成功と見なされて終了するためには、プライマリと少なくとも 1 つのセカンダリでアクティビティがトランザクション ログに書き込まれたことが確認される必要があります。ノードで障害が発生した場合は、データベースがいずれかのセカンダリ レプリカに自動的にフェールオーバーします。 これにより、クライアント アプリケーションに対する一時的な接続の中断が発生します。 したがって、Azure SQL Database のすべてのクライアントがなんらかの形式の一時的な接続処理を実装する必要があります。 詳細については、「 [再試行サービス固有のガイダンス](/azure/best-practices-retry-service-specific/)」を参照してください。

#### <a name="resource-management"></a>リソース管理
各データベースの作成時にサイズの上限を構成します。 現在利用できる最大サイズは 1 TB です。サイズの上限は、ご利用のサービス レベルによって異なります。[Azure SQL データベースのサービス プランとパフォーマンス レベル](/azure/sql-database/sql-database-resource-limits/#service-tiers-and-performance-levels)に関するページをご覧ください。 データベースのサイズが上限に達すると、追加の INSERT コマンドや UPDATE コマンドが拒否されるようになります  (データのクエリと削除は引き続き実行できます)。

データベース内では、Azure SQL Database はリソースの管理にファブリックを使用します。 ただし、障害の検出には、ファブリック コントローラーではなく、リング型トポロジを使用します。 クラスター内の各レプリカには 2 つのネイバーがあり、レプリカはネイバーに障害が発生すると検出します。 レプリカに障害が発生した場合は、そのネイバーが Reconfiguration Agent をトリガーして、別のコンピューターでレプリカを再作成するようにします。 エンジン調整は、論理サーバーがコンピューター上の過大なリソースを使用しないようにするため、またはコンピューターの物理的制限を超えないようにするために用意されています。

### <a name="elasticity"></a>弾力性
アプリケーションが 1 TB のデータベースの上限値を超える要求を行う場合は、スケールアウト アプローチを実装する必要があります。 Azure SQL Database でのスケールアウトは、複数の SQL データベース間でデータを手動でパーティション分割することで行います。この方法はシャーディングと呼ばれています。 このスケールアウト アプローチでは、スケールに対して線形に近い形で増加するコストを実現できます。 柔軟な増加 (キャパシティ オン デマンド) により、容量とコストを必要に応じて増やすことができます。これは、データベースが使用可能な最大サイズではなく、1 日あたりに使用される実際の平均サイズに基づいて課金されるためです。

## <a name="sql-server-on-virtual-machines"></a>Virtual Machines 上の SQL Server
Azure Virtual Machines に SQL Server (バージョン 2014 以降) をインストールすると、SQL Server の従来の可用性機能を利用することができます。 たとえば、AlwaysOn 可用性グループ、データベース ミラーリングなどの機能です。 Azure VM、ストレージ、およびネットワークは、オンプレミスの非仮想化 IT インフラストラクチャとは異なる動作特性を持ちます。 Azure 内に高可用性/ディザスター リカバリー (HA/DR) SQL Server ソリューションを正常に実装するには、これらの違いを理解し、その違いを反映するようにソリューションを設計する必要があります。

### <a name="high-availability-nodes-in-an-availability-set"></a>可用性セット内の高可用性ノード
Azure に高可用性ソリューションを実装した場合、Azure 内の可用性セットを使用して、高可用性ノードを別個の障害ドメインおよびアップグレード ドメインに配置できます。 簡単に言うと、可用性セットは Azure の 1 つの概念です。 これは、AlwaysOn 可用性グループ、データベース ミラーリングなどの方法を問わず、データベースが実際に高可用性を備えるために従う必要があるベスト プラクティスです。 このベスト プラクティスに従っていない場合は、システムが高可用性を備えていると誤解する可能性があります。 ところが、実際のところ、すべてのノードは Azure リージョンの同じ障害ドメインに配置されていれば、同時に停止する可能性があります。

この推奨事項は、ログ配布には適用されません。 ディザスター リカバリー機能として、すべてのサーバーが Azure の別々のリージョンで実行されるようにする必要があるためです。 当然、これらのリージョンは別個の障害ドメインです。

クラシック ポータルでデプロイされた Azure Cloud Services の一連の VM を同じ可用性セットに含めるためには、それらを同じクラウド サービスにデプロイする必要があります。 Azure Resource Manager (現在のポータル) を介してデプロイされた VM には、このような制限はありません。 クラシック ポータルで Azure クラウド サービスにデプロイされた VM の場合、同じ可用性セットに参加できるのは、同じクラウド サービス内のノードだけです。 さらに、Cloud Services の一連の VM はサービス復旧後も IP を維持するために、同じ仮想ネットワーク内にある必要があります。 これにより、DNS の更新の中断を回避できます。

### <a name="azure-only-high-availability-solutions"></a>Azure のみ: 高可用性ソリューション
Azure 内の SQL Server データベースの高可用性ソリューションを実現するには、AlwaysOn 可用性グループまたはデータベース ミラーリングを使用します。

次の図は、Azure Virtual Machines で実行している AlwaysOn 可用性グループのアーキテクチャを示しています。 この図は、このテーマについての詳細な解説記事「[Azure 仮想マシンにおける SQL Server の高可用性とディザスター リカバリー](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr/)」から引用したものです。

![Microsoft Azure AlwaysOn 可用性グループ](./images/technical-guidance-recovery-local-failures/high_availability_solutions-1.png)

また、Azure ポータルの AlwaysOn テンプレートを使用して、Azure VM で AlwaysOn 可用性グループのデプロイをエンドツーエンドで自動的にプロビジョニングすることもできます。 詳細については、[Microsoft Azure Portal ギャラリーで提供されている SQL Server AlwaysOn テンプレート](https://blogs.technet.microsoft.com/dataplatforminsider/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery/)に関する記事を参照してください。

次の図は、Azure Virtual Machines でのデータベース ミラーリングの使用方法を示しています。 この図も、[Azure 仮想マシンにおける SQL Server の高可用性とディザスター リカバリー](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr/)に関する詳細な解説記事から引用したものです。

![Database mirroring in Microsoft Azure](./images/technical-guidance-recovery-local-failures/high_availability_solutions-2.png)

> [!NOTE]
> 両方のアーキテクチャにドメイン コントローラーが必要です。 ただし、データベース ミラーリングでサーバー証明書を使用して、ドメイン コントローラーを不要にすることができます。
> 
> 

## <a name="other-azure-platform-services"></a>その他の Azure プラットフォーム サービス
Azure に構築されるアプリケーションは、ローカル障害から復旧するためのプラットフォーム機能を利用できます。 場合によっては、特定の処理を行って特定のシナリオでの可用性を高めることができます。

### <a name="service-bus"></a>Service Bus
Azure Service Bus の一時的な機能停止状態を緩和するには、永続的なクライアント側キューの作成を検討してください。 これにより、一時的に代替の、ローカル ストレージ メカニズムを使用して、Service Bus キューに追加できないメッセージを保存します。 アプリケーションでは、サービスを復元した後で、一時的に保存されたメッセージの処理方法を決定することができます。 詳細については、「[Service Bus の仲介型メッセージングを使用したパフォーマンス向上のためのベスト プラクティス](/azure/service-bus-messaging/service-bus-performance-improvements/)」と [Service Bus (障害復旧)](recovery-loss-azure-region.md#other-azure-platform-services) に関するページをご覧ください。

### <a name="hdinsight"></a>HDInsight
Azure HDInsight に関連付けられているデータは、既定では Azure Blob Storage に保存されます。 Azure Storage は、Blob Storage に対して高可用性と持続性のプロパティを指定します。 Hadoop MapReduce ジョブに関連付けられたマルチノード処理は、HDInsight が必要とする場合にプロビジョニングされる一時的な Hadoop 分散ファイル システム (HDFS) 上で発生します。 MapReduce ジョブの結果も、既定では Azure BLOB ストレージに保存されます。そのため、Hadoop クラスターがプロビジョニング解除された後も、処理されたデータには持続性があり、高可用性が保たれます。 詳細については、[HDInsight (障害復旧)](recovery-loss-azure-region.md#other-azure-platform-services) に関するページをご覧ください。

## <a name="checklists-for-local-failures"></a>ローカル障害のチェックリスト
### <a name="cloud-services"></a>Cloud Services
1. このドキュメントの「Cloud Services」セクションを確認する。
2. 各ロールに 2 つ以上のインスタンスを構成する。
3. ロール インスタンスではなく、持続性のあるストレージで状態を永続化する。
4. StatusCheck イベントを適切に処理する。
5. 可能であれば、トランザクションの関連する変更をラップする。
6. worker ロール タスクがべき等で再起動可能であることを確認する。
7. 成功するまで操作を呼び出し続ける。
8. 自動スケールの方法を検討する。

### <a name="virtual-machines"></a>Virtual Machines
1. このドキュメントの「Virtual Machines」セクションを確認する。
2. 永続的ストレージに D ドライブを使用しない。
3. サービス層のコンピューターを可用性セットにグループ化する。
4. 負荷分散とオプションのプローブを構成する。

### <a name="storage"></a>Storage
1. このドキュメントの「Storage」セクションを確認する。
2. データまたは帯域幅がクォータを超えている場合、複数のストレージ アカウントを使用する。

### <a name="sql-database"></a>SQL Database
1. このドキュメントの「SQL Database」セクションを確認する。
2. 一時的なエラーを処理するために再試行ポリシーを実行する。
3. スケールアウトの方法として、パーティション分割/シャーディングを使用する。

### <a name="sql-server-on-virtual-machines"></a>Virtual Machines 上の SQL Server
1. このドキュメントの「Virtual Machines 上の SQL Server」セクションを確認する。
2. Virtual Machines についての前の推奨事項に従う。
3. AlwaysOn など、SQL Server の高可用性機能を使用する。

### <a name="service-bus"></a>Service Bus
1. このドキュメントの「Service Bus」セクションを確認する。
2. バックアップとして永続的なクライアント側キューを作成することを検討する。

### <a name="hdinsight"></a>HDInsight
1. このドキュメントの「HDInsight」セクションを確認する。
2. ローカル障害に必要な追加の可用性の手順がない。
