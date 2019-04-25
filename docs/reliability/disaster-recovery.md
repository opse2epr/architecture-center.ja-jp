---
title: Azure アプリケーションの障害とディザスター リカバリー
description: Azure でのディザスター リカバリー アプローチの概要
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: f00341b06b8092c798d6260317226190cbace66b
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59642877"
---
# <a name="failure-and-disaster-recovery-for-azure-applications"></a>Azure アプリケーションの障害とディザスター リカバリー

*ディザスター リカバリー*は、致命的な損失の発生時にアプリケーション機能を復元するプロセスです。

障害発生時の機能の制限に対する許容範囲に基づいて、アプリケーションごとに異なるビジネス上の意思決定を行います。 アプリケーションによっては、一定期間、完全に使用できなくなるか、または機能の制限や処理の遅延がありながら部分的に使用できることが許容される場合があります。 また、機能の制限が許容されないアプリケーションもあります。

## <a name="disaster-recovery-plan"></a>ディザスター リカバリー プラン

まずはリカバリー プランの作成から開始します。 プランは、完全にテストした後に完成と見なされます。 顧客向けに定義したサービス レベル アグリーメント (SLA) 内で機能を復元するために必要な人員、プロセス、およびアプリケーションを含めます。

ディザスター リカバリー プランを作成してテストする際には、次の推奨事項を考慮してください。

- プランに、サポートへの連絡と問題のエスカレーションのプロセスを含めます。 この情報は、初めて復旧プロセスを実行する際に、長時間にわたるダウンタイムを回避するのに役立ちます。
- アプリケーションの障害によるビジネスの影響を評価します。
- ミッションクリティカルなアプリケーションに対応できる、複数のリージョンにわたるディザスター リカバリー アーキテクチャを選択します。
- 自動化やテストも含めたディザスター リカバリー プランの特定のオーナーを指定します。
- 特にすべての手動の手順について、プロセスを文書化します。
- 可能な限りプロセスを自動化します。
- すべての参照データとトランザクション データに対してバックアップ戦略を確立し、これらのバックアップの復元を定期的にテストします。
- アプリケーションによって使用される Azure サービスのスタックに対して、アラートを設定します。
- プランを実行する運用スタッフをトレーニングします。
- プランを検証し改善するために、定期的な障害のシミュレーションを実行します。

[Azure Site Recovery](/azure/site-recovery/) を使用して仮想マシン (VM) をレプリケートする場合は、完全に自動化されたリカバリー プランを作成して、アプリケーション全体をフェールオーバーします。

## <a name="manual-responses"></a>手動での対応

自動化が最も適切ですが、ディザスター リカバリーのいくつかの戦略には手動の対応が必要です。

### <a name="alerts"></a>アラート

アプリケーションで、積極的な介入が必要な可能性がある警告の兆候を監視します。 たとえば、Azure SQL Database または Azure Cosmos DB でアプリケーションが常に調整されている場合は、データベース容量の増加や、クエリの最適化が必要な可能性があります。 アプリケーションで調整エラーが透過的に処理される場合でも、テレメトリでは、フォロー アップのためにアラートを生成する必要があります。

サービスの制限とクォータのしきい値について、Azure リソースのメトリックと診断ログのアラートを構成することをお勧めします。 可能であれば、メトリックのアラートを、診断ログよりも待機時間が短くなるよう設定します。

 [Resource Health](/azure/service-health/resource-health-checks-resource-types) を通じて、Azure では Azure サービスの調整問題を診断するのに役立つ、いくつかの組み込みの正常性状態チェックを提供しています。

### <a name="failover"></a>フェールオーバー

各 Azure アプリケーションとその Azure サービスのディザスター リカバリー戦略を構成します。 ディザスター リカバリーをサポートするために使用できるデプロイ戦略は、各アプリケーションのすべてのコンポーネントに必要な SLA に応じて異なる場合があります。  

Azure では、手動のフェールオーバー ( [Redis Cache の geo レプリカ](/azure/azure-cache-for-redis/cache-how-to-geo-replication)など) や、自動フェールオーバー ( [SQL 自動フェールオーバー グループ](/azure/sql-database/sql-database-auto-failover-group)など) のために、多くの Azure サービス内でさまざまな機能を提供しています。 例: 

- 主に仮想マシンを使用するアプリケーションでは、Web 層とロジック層で Azure Site Recovery を使用できます。 詳細については、[Azure から Azure へのディザスター リカバリー アーキテクチャ](/azure/site-recovery/azure-to-azure-architecture)に関するページを参照してください。 VM 上の SQL Server では、[SQL Server Always On 可用性グループ](/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr)を使用します。
- App Service および Azure SQL Database を使用するアプリケーションでは、セカンダリ リージョン内で構成されている小規模な層の App Service プランを使用でき、これはフェールオーバーの発生時に自動スケーリングされます。 データベース層に対してフェールオーバー グループを使用します。

どちらのシナリオでも、 [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview)  プロファイルにより、リージョン間での自動フェールオーバーが行われます。 フェールオーバー時の可用性を迅速化するために、セカンダリ リージョン内で[ロード バランサー](/azure/load-balancer/load-balancer-overview)または [アプリケーション ゲートウェイ](/azure/application-gateway/overview) を設定する必要があります。

### <a name="operational-readiness-testing"></a>運用準備状況のテスト

セカンダリ リージョンへのフェールオーバーと、プライマリ リージョンへのフェールバックについて、運用準備状況のテストを実行します。 多くの Azure サービスでは、ディザスター リカバリー訓練のために手動フェールオーバーまたはテスト フェールオーバーをサポートしています。 または、Azure サービスをシャットダウンまたは削除することで、停止をシミュレートできます。

## <a name="application-failure"></a>アプリケーションの障害

アプリケーションの障害には、復旧可能なものと復旧不可能なものがあります。 復旧可能なエラーは軽減できますが、復旧不可能なエラーではアプリケーションが停止します。

- 一部の障害は、エラーを自動的に処理し、代替アクションを講じることで、透過的に対処できます。 たとえば、Traffic Manager では、ホスト仮想マシンの基になるハードウェアまたはオペレーティング システム ソフトウェアに起因する障害が自動的に処理されます。
- 一部のエラーでは、アプリケーションで機能を制限しながらユーザー要求の処理を続行できます。
- より重大なサービスの中断では、アプリケーションを使用できなくなる場合があります。

適切に設計されたシステムでは、設計時と実行時にサービス レベルでの責任が分離されます。 この分離により、依存サービスの中断によってアプリケーション全体がダウンするのを防ぐことができます。 たとえば、次のモジュールを持つ Web コマース アプリケーションがあるとします。

![アートにリンクを追加](./_images/disaster-recovery.png)

注文をホストするためのデータベースで障害が発生した場合、注文処理サービスでは販売トランザクションを処理できなくなります。 アーキテクチャによっては、注文送信サービスと注文処理サービスの続行が不可能になることがあります。 ただし、製品データが別の場所に格納されていれば、アプリケーションの他の部分が利用できない場合でも、製品カタログは引き続き使用できます。

お客様自身が、アプリケーションでユーザーに一時的な問題を通知する方法を決定し、システムに適切な通知を組み込むことができます。 前の例では、製品を表示したり、ショッピング カートに追加したりできるかもしれません。 ただし、顧客が購入しようとすると、アプリケーションで注文機能が一時的に利用不可であることが通知されます。 このアプローチは顧客にとって最善ではありませんが、アプリケーション全体のサービス中断は回避されます。

## <a name="data-corruption-and-restoration"></a>データの破損と復元

データ ストアで障害が発生した場合、特にデータがレプリケートされた場合には、データ ストアが再び使用可能な状態になるときにデータに不整合性が生じることがあります。 レプリケートされたデータ ストアの目標復旧時間 (RTO) と目標復旧時点 (RPO) を理解しておくと、データの損失量を予測することができます。

リージョン間フェールオーバーが手動で開始されるか、または Microsoft によって開始されるかについては、Azure サービスの SLA を確認してください。 リージョン間フェールオーバーに関する SLA なしのサービスでは、一般的に Microsoft によってフェールオーバーのタイミングが決定され、通常はプライマリ リージョン内のデータの復旧が優先されます。 プライマリ リージョン内のデータが復旧不可能と見なされる場合、Microsoft では、セカンダリ リージョンにフェールオーバーします。

### <a name="restoring-data-from-backups"></a>バックアップからのデータの復元

バックアップでは、意図しない削除やデータの破損によってアプリケーションのコンポーネントが失われることから保護します。 これにより、以前のコンポーネントの機能バージョンが維持され、これを使用して以前の状態に復元できます。

ディザスター リカバリー戦略は、バックアップに代わるものではありませんが、アプリケーション データの定期的なバックアップにより、いくつかのディザスター リカバリーのシナリオがサポートされます。 バックアップ ストレージは、ディザスター リカバリー戦略に基づいて選択する必要があります。

バックアップ プロセスを実行する頻度により、RPO が決定されます。 たとえば、1 時間ごとにデータをバックアップする場合、バックアップの 2 分前に障害が発生すると、58 分間のデータが失われます。 ディザスター リカバリー プランには、データの損失に対処する方法を含める必要があります。

一般的には、1 つのデータ ストア内のデータが別のストア内のデータを参照するようにします。 たとえば、Azure Storage の BLOB にリンクしている列を含む SQL データベースがあるものとします。 バックアップが同時に実行されない場合、データベースには、障害発生前にバックアップされなかった BLOB へのポインターが含まれる可能性があります。 アプリケーションまたはディザスター リカバリー プランでは、復旧後のこのような不整合を処理するプロセスを実装する必要があります。

> [!NOTE]
> [Azure Backup](/azure/backup/backup-azure-vms-first-look-arm) を使用してバックアップされる VM など、いくつかのシナリオでは、同じリージョン内のバックアップからのみ復元できます。 [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-how-to-geo-replication) などのその他の Azure サービスでは、geo レプリケートされたバックアップが提供されており、これによりリージョン間でサービスを復元できます。

### <a name="azure-storage-and-azure-sql-database"></a>Azure Storage と Azure SQL Database

Azure では、Azure Storage と SQL Database のデータが、同じリージョン内の異なる障害ドメイン内に自動的に 3 回格納されます。 geo レプリケーションを使用している場合は、さらに 3 回、異なるリージョンに格納します。 ただし、(たとえば、ユーザー エラーによって) プライマリ コピー内でデータが破損したり削除されている場合、変更は他のコピーにレプリケートされます。

潜在的なデータの破損または削除を管理するには 2 つのオプションがあります。

- **カスタム バックアップ戦略を作成します。** ビジネス要件やガバナンス規定に応じて、Azure またはオンプレミスにバックアップを格納できます。
- **ポイントインタイム リストア オプション**を使用して SQL Database を復旧します。

#### <a name="azure-storage-recovery"></a>Azure Storage の復旧

Azure Storage のカスタム バックアップ プロセスを開発するか、または多くのサード パーティ製バックアップ ツールのいずれかを使用することができます。

Azure Storage では、自動レプリカによるデータの回復性が提供されますが、アプリケーション コードやユーザーによるデータの破損は回避されません。 アプリケーション エラーやユーザー エラーの後にデータの忠実性を維持するには、監査ログと共にセカンダリ ストレージの場所にデータをコピーするなどの高度な手法が必要です。 いくつかのオプションがあります。

- [**ブロック BLOB。**](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) 各ブロック BLOB の特定時点のスナップショットを作成します。 各スナップショットについて、BLOB 内で前回のスナップショットの状態との差分を保存するために必要なストレージのみに課金されます。 スナップショットは元の BLOB に依存するため、別の BLOB または別のストレージ アカウントにコピーすることをお勧めします。 このアプローチにより、バックアップ データを誤って削除してしまうことから確実に保護できます。 別のストレージ アカウントに BLOB をコピーするには、[AzCopy](/azure/storage/common/storage-use-azcopy) または [Azure PowerShell](/azure/storage/common/storage-powershell-guide-full) を使用します。

    詳細については、「 [BLOB のスナップショットの作成](/rest/api/storageservices/creating-a-snapshot-of-a-blob)」を参照してください。

- [**Azure Files。**](/azure/storage/files/storage-files-introduction) [共有のスナップショット](/azure/storage/files/storage-snapshots-files)、AzCopy、または PowerShell を使用して、別のストレージ アカウントにファイルをコピーします。
- [**Azure ストレージ テーブル。**](/azure/storage/tables/table-storage-overview) AzCopy を使用して、他のリージョンの別のストレージ アカウントにテーブル データをエクスポートします。

#### <a name="sql-database-recovery"></a>SQL Database の復旧

データ損失からビジネスを守るために、SQL Database では、データベースの完全バックアップ (毎週)、データベースの差分バックアップ (1 時間ごと)、およびトランザクション ログのバックアップ (5 から 10 分ごと) を組み合わせて自動的に実行します。 Basic、Standard、および Premium の SQL Database 階層では、ポイントインタイム リストアを使用してデータベースを以前の状態に復元します。 詳細については、次の記事を参照してください。

- [データベースの自動バックアップを使用した Azure SQL Database の復旧](/azure/sql-database/sql-database-recovery-using-backups)
- [Azure SQL Database によるビジネス継続性の概要](/azure/sql-database/sql-database-business-continuity)

もう 1 つのオプションとしては、SQL Database 用のアクティブ geo レプリケーションを使用します。この場合は、データベースの変更が、Azure の同じリージョンまたは別のリージョン内のセカンダリ データベースに自動的にレプリケートされます。 詳細については、「[アクティブ geo レプリケーションの作成と使用](/azure/sql-database/sql-database-active-geo-replication)」を参照してください。

もっと手動的なバックアップおよび復元の方法を使用することもできます。

- **DATABASE COPY** コマンドを使用して、トランザクションの一貫性を保つ、データベースのバックアップ コピーを作成できます。
- Azure SQL Database のインポート/エクスポート サービスを使用します。このサービスでは、Azure Blob Storage に格納されている BACPAC ファイル (ご利用のデータベース スキーマおよび関連するデータを含む圧縮されたファイル) へのデータベースのエクスポートがサポートされます。 リージョン全体のサービス中断を防ぐには、BACPAC ファイルを代替リージョンにコピーします。

### <a name="sql-server-on-vms"></a>VM 上の SQL Server

VM 上で実行される SQL Server の場合、従来のバックアップとログ配布の 2 つのオプションがあります。

- 従来のバックアップでは、特定の時点の状態に復元できますが、復旧プロセスに時間がかかります。 従来のバックアップを復元するには、最初に完全バックアップから開始し、その後すべての増分バックアップを適用する必要があります。
- ログ バックアップの復元を遅延させるには、ログ配布セッションを構成します。 これにより、プライマリ レプリカで発生したエラーから復旧するための時間が確保されます。

### <a name="azure-database-for-mysql-and-azure-database-for-postgresql"></a>Azure Database for MySQL と Azure Database for PostgreSQL

Azure Database for MySQL と Azure Database for PostgreSQL では、データベース サービスで 5 分ごとにバックアップが自動的に作成されます。 これらの自動バックアップを使用すると、以前の時点のサーバーとそのデータベースが新しいサーバーに復元されます。 詳細については、次を参照してください。

- [Azure Portal を使用して Azure Database for MySQL サーバーのバックアップと復元を行う方法](/azure/mysql/howto-restore-server-portal)
- [Azure portal を使用して Azure Database for PostgreSQL のサーバーをバックアップおよび復元する方法](/azure/postgresql/howto-restore-server-portal)

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Cosmos DB では、一定の間隔でバックアップが自動的に作成されます。 バックアップは別のストレージ サービスに個別に保存され、リージョンの障害から保護するためにグローバルにレプリケートされます。 データベースまたはコレクションを誤って削除した場合は、サポート チケットを申請するか、Azure サポートに連絡して、最新の自動バックアップからデータを復元できます。 詳細については、「[Azure Cosmos DB でのオンライン バックアップとオンデマンドのデータ復元](/azure/cosmos-db/online-backup-and-restore)」を参照してください。

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Azure Virtual Machines をアプリケーション エラーや誤削除から保護するには、[Azure Backup](/azure/backup/) を使用します。 作成したバックアップは、複数の VM ディスク間で一貫性を保ちます。 さらに、Azure Backup コンテナーをリージョン間でレプリケートして、リージョンの損失からの復旧をサポートできます。

## <a name="network-outage"></a>ネットワークの停止

Azure ネットワークの一部にアクセスできないと、アプリケーションやデータにアクセスできなくなる可能性があります。 このような状況では、機能を制限してほとんどのアプリケーションを実行するようにディザスター リカバリー戦略を設計することをお勧めします。

機能の制限を使用できない場合に残されているオプションは、アプリケーションのダウンタイムまたは代替リージョンへのフェールオーバーです。

機能制限のシナリオ:

- Azure ネットワークの停止のためにアプリケーションがデータにアクセスできない場合でも、キャッシュされているデータを使用すれば、アプリケーションの機能を制限した状態でローカルに実行できる可能性があります。
- 接続が回復するまで別の場所にデータを格納できる可能性があります。

## <a name="dependent-service-failure"></a>依存サービスの障害

各依存サービスについて、サービス中断による影響、およびアプリケーションがどのように応答するかを理解する必要があります。 多くのサービスには、回復性と可用性をサポートする機能が含まれるため、各サービスを個別に評価することで、ディザスター リカバリー プランが向上する可能性があります。 たとえば、Azure Event Hubs では、セカンダリ名前空間への[フェールオーバー](/azure/event-hubs/event-hubs-geo-dr#setup-and-failover-flow)がサポートされています。

## <a name="region-wide-service-disruptions"></a>リージョン全体にわたるサービス中断

多くの障害は、同じ Azure リージョン内で管理できます。 ただし、リージョン全体にわたるサービス中断が万一発生した場合、データのローカルな冗長コピーは使用できません。 geo レプリケーションを有効にしてある場合は、BLOB とテーブルのコピーがさらに 3 つ、別のリージョン内に存在します。 Microsoft によってリージョンの損失が宣言された場合、Azure では、セカンダリ リージョンにすべての DNS エントリが再マッピングされます。

> [!NOTE]
> このプロセスは、リージョン全体にわたるサービス中断の場合にのみ発生し、ユーザーによるコントロールの範疇にありません。 RPO や RTO を向上させるには、[Azure Site Recovery](/azure/site-recovery/) を使うことを検討してください。 Site Recovery を使うことで、許容できる障害と、レプリケートされた VM にフェールオーバーするタイミングを判断できます。

リージョン全体にわたるサービス中断への対応は、デプロイとディザスター リカバリー プランによって異なります。

- 保証された復旧時間を必要としない重要性の低いアプリケーションの場合は、コスト管理戦略として、別のリージョンに再デプロイすることが有意義と考えられます。
- デプロイされたロールと共に別のリージョンでホストされているが、リージョン間でトラフィックを分散しないアプリケーション (*アクティブ/パッシブ デプロイ*) の場合は、代替リージョン内のセカンダリ ホステッド サービスに切り替えます。
- 別のリージョン内にフルスケールのセカンダリ デプロイが存在するアプリケーション (*アクティブ/アクティブ デプロイ*) の場合は、そのリージョンにトラフィックをルーティングします。

リージョン全体にわたるサービス中断からの復旧の詳細については、「[リージョン全体でのサービスの中断から回復する](../resiliency/recovery-loss-azure-region.md)」を参照してください。

### <a name="vm-recovery"></a>VM の復旧

重要なアプリについては、リージョン全体にわたるサービス中断に備えて VM の復旧を計画します。

- Azure Backup または別のバッグアップ方法を使用して、アプリケーション整合性のある、複数のリージョンにまたがるバックアップを作成します  (バックアップ コンテナーのレプリケーションは作成時に構成する必要があります)。
- Site Recovery を使用して、ワンクリックでのアプリケーション フェールオーバーとフェールオーバー テスト用にリージョン間でレプリケートします。
- Traffic Manager を使用して、別のリージョンへのユーザー トラフィック フェールオーバーを自動化します。

詳細については、[「リージョン全体でのサービスの中断から回復する」の「仮想マシン」](../resiliency/recovery-loss-azure-region.md#virtual-machines)を参照してください。

### <a name="storage-recovery"></a>ストレージの復旧

リージョン全体にわたるサービス中断の発生時にストレージを保護するには:

- geo 冗長ストレージを使用します。
- ストレージが geo レプリケートされている場所を認識しておきます。 この場所は、ストレージと同じリージョンにある必要があるデータの他のインスタンスをデプロイする場所に影響を与えます。
- フェールオーバー後にデータの整合性を確認し、必要に応じてバックアップから復元します。

詳細については、「[RA-GRS を使用した高可用性アプリケーションの設計](/azure/storage/common/storage-designing-ha-apps-with-ragrs)」を参照してください。

### <a name="sql-database-and-sql-server"></a>SQL Database と SQL Server

Azure SQL Database では、

- geo リストアを使用して、別のリージョン内のバックアップ コピーからデータベースを復元します。 詳細については、「[データベースの自動バックアップを使用した Azure SQL Database の復旧](/azure/sql-database/sql-database-recovery-using-backups)」を参照してください。
- アクティブ geo レプリケーションを使用して、セカンダリ データベースにフェールオーバーします。 詳細については、「[アクティブ geo レプリケーションの作成と使用](/azure/sql-database/sql-database-active-geo-replication)」を参照してください。

VM で実行される SQL Server の詳細については、「[Azure 仮想マシンにおける SQL Server の高可用性とディザスター リカバリー](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr/)」を参照してください。

## <a name="service-specific-guidance"></a>サービス固有のガイダンス

次の記事では、特定の Azure サービスに対するディザスター リカバリーついて説明します。

| Service                       | 記事                                                                                                                                                                                       |
|-------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Database for MySQL      | [Azure Database for MySQL でのビジネス継続性の概要](/azure/mysql/concepts-business-continuity)                                                  |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL でのビジネス継続性の概要](/azure/postgresql/concepts-business-continuity)                                        |
| Azure クラウド サービス          | [Azure Cloud Services に影響を与える Azure サービスの中断が発生した場合の対処方法](/azure/cloud-services/cloud-services-disaster-recovery-guidance) |
| Cosmos DB                     | [Azure Cosmos DB での高可用性](/azure/cosmos-db/high-availability)                                                                                |
| Azure Key Vault               | [Azure Key Vault の可用性と冗長性](/azure/key-vault/key-vault-disaster-recovery-guidance)                                                        |
| Azure Storage                 | [Azure Storage でのディザスター リカバリーとストレージ アカウントのフェールオーバー (プレビュー)](/azure/storage/common/storage-disaster-recovery-guidance)                       |
| SQL Database                  | [Azure SQL Database を復元する、またはセカンダリにフェールオーバーする](/azure/sql-database/sql-database-disaster-recovery)                                       |
| Virtual Machines              | [Azure サービスの中断が Azure Cloud に影響を与える場合の対処方法](/azure/cloud-services/cloud-services-disaster-recovery-guidance)               |
| Azure Virtual Network         | [Virtual Network – ビジネス継続性](/azure/virtual-network/virtual-network-disaster-recovery-guidance)                                                  |

## <a name="next-steps"></a>次の手順

- [データの破損または偶発的な削除から復旧する](../resiliency/recovery-data-corruption.md)
- [リージョン全体でのサービスの中断から回復する](../resiliency/recovery-loss-azure-region.md)