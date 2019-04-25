---
title: Azure で信頼性に対するアプリケーションの正常性を監視する
description: Azure で監視を使用してアプリケーションの信頼性を向上させる方法
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: 46f9f3fb623a2facf4b9f9c6ec57376ae59e41dc
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59642887"
---
# <a name="monitoring-application-health-for-reliability-in-azure"></a>Azure で信頼性に対するアプリケーションの正常性を監視する

監視と診断は回復性にとって非常に重要です。 何かが失敗した場合、失敗した "*こと*"、失敗した "*とき*"、失敗した "*理由*" を知る必要があります。

"*監視*" は "*障害の検出*" と同じではありません。 たとえば、アプリケーションでは、一時的なエラーを検出して再試行し、ダウンタイムを回避することがあります。 それでも、再試行操作をログに記録して、エラー率を監視し、アプリケーションの正常性の全体像を把握できるようにすることも必要です。

監視と診断のプロセスは、4 つの異なるステージがあるパイプラインと考えることができます: インストルメンテーション、収集と保存、分析と診断、視覚化とアラート。

## <a name="instrumentation"></a>インストルメンテーション

アプリケーションを直接監視するのは実際的ではないので、インストルメンテーションが重要です。 大規模な分散システムでは、数十の仮想マシン (VM) で実行され、時間と共にそれが追加されたり削除されたりする可能性があります。 同様に、クラウド アプリケーションでは、多数のデータ ストアが使用され、1 つのユーザー アクションが複数のサブシステムにまたがる場合があります。

リッチなインストルメンテーションを提供します。

- 可能性は高いけれども、まだ発生していない障害については、原因を特定し、状況を緩和して、システムを引き続き使用できるようにするのに十分なデータを提供します。
- 既に発生している障害については、アプリケーションでユーザーに適切なエラー メッセージを返しながらも、低下した機能であっても実行の継続を試みる必要があります。

監視システムでは、包括的な詳細をキャプチャして、アプリケーションを効率的に復元できるようにし、必要な場合は、設計者や開発者が状況の再発を防ぐためにシステムを変更できるようにする必要があります。

監視用の生データは、次のようなさまざまなソースから取得できます。

- アプリケーション ログ。[Azure Application Insights](/azure/azure-monitor/app/app-insights-overview) サービスによって生成されるものなど。
- [Azure 監視エージェント](/azure/azure-monitor/platform/agents-overview)によって収集されるオペレーティング システムのパフォーマンス メトリック。
- [Azure リソース](/azure/azure-monitor/platform/metrics-supported)。Azure Monitor によって収集されるメトリックなど。
- [Azure Service Health](/azure/service-health/service-health-overview)。アクティブなイベントの追跡に役立つダッシュボードが提供されます。
- Azure プラットフォームに組み込まれている [Azure AD ログ](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) 。

ほとんどの Azure サービスにはメトリックと診断機能があり、問題の原因を分析して特定するために構成できます。 詳細については、[Azure Monitor によって収集される監視データ](/azure/azure-monitor/platform/data-collection)に関する記事を参照してください。

## <a name="collection-and-storage"></a>収集と保存

生のインストルメンテーション データは、次のようなさまざまな場所と形式で保持できます。

- アプリケーション トレース ログ
- IIS ログ
- パフォーマンス カウンター

これらのさまざまなソースは収集され、統合されて、Application Insights、Azure Monitor メトリック、Service Health、ストレージ アカウント、Azure Log Analytics など、Azure 内の信頼できるデータ ストアに配置されます。

## <a name="analysis-and-diagnosis"></a>分析と診断

これらのデータ ストアに統合されたデータを分析して、問題のトラブルシューティングを行い、アプリケーションの正常性の全体像を把握します。 一般に、Kusto クエリを使って Application Insights や Log Analytics 内のデータを[検索して分析](/azure/azure-monitor/log-query/log-query-overview)したり、 [管理ソリューション](/azure/azure-monitor/insights/solutions-inventory)を使って事前構成済みのグラフを表示したりできます。 または、Azure Advisor を使って、[回復性](/azure/advisor/advisor-high-availability-recommendations)と[パフォーマンス](/azure/advisor/advisor-performance-recommendations)に重点を置いた推奨事項を表示します。

## <a name="visualization-and-alerts"></a>視覚化とアラート

ダッシュボードやメール アラートなど、オペレーターが簡単に問題や傾向を把握できる形式でテレメトリ データを提供します。

 [Azure ダッシュボード](/azure/azure-portal/azure-portal-dashboards)を使ってアプリケーションの状態のフルスタック ビューを取得し、Application Insights、Log Analytics、Azure Monitor のメトリック、Service Health から監視グラフの統合ビューを作成します。 そして、 [Azure Monitor のアラート](/azure/azure-monitor/platform/alerts-overview)を使って、Service Health、リソースの正常性、Azure Monitor のメトリック、Log Analytics のログ、Application Insights についての通知を作成します。

監視と診断の詳細については、「[監視と診断](../best-practices/monitoring.md)」を参照してください。

## <a name="health-probes-and-check-functions"></a>正常性プローブとチェック機能

アプリケーションの正常性とパフォーマンスは、時間の経過と共に低下することがあり、アプリケーションで障害が発生するまで低下に気づかない場合があります。

プローブまたはチェック機能を実装し、アプリケーションの外部から定期的にそれらを実行します。 このようなチェックは、アプリケーション全体、アプリケーションの個々の部分、アプリケーションで使用する特定のサービス、または個別のコンポーネントについての、応答時間を測定するような簡単なものの場合もあります。

チェック機能では、プロセスを実行することで、有効な結果が生成されることを確認したり、待機時間の測定や可用性の確認を行ったり、システムから情報を抽出したりできます。

## <a name="long-running-workflow-failures"></a>実行時間の長いワークフローの障害

実行時間の長いワークフローには、それぞれが独立している必要のある複数のステップが含まれることがよくあります。

実行時間の長いプロセスの進行を監視して、ワークフロー全体のロールバックが必要になる可能性、または複数の補正トランザクションの実行が必要になる可能性を、最小限に抑えます。

>[!TIP]
> [Scheduler Agent Supervisor](../patterns/scheduler-agent-supervisor.md) などのパターンを実装して、実行時間の長いワークフローの進行状況を監視し、管理します。

## <a name="application-logs"></a>アプリケーション ログ

アプリケーション ログは、診断データの重要なソースです。 最も必要なときに分析情報を取得するには、アプリケーション ログ記録のベスト プラクティスに従います。

### <a name="log-data-in-the-production-environment"></a>運用環境のデータのログを記録する

運用状態で問題の原因を診断するのに十分な情報が得られるように、運用環境でアプリケーションを実行しながら、確実な利用統計情報をキャプチャします。

### <a name="log-events-at-service-boundaries"></a>サービスの境界でイベントのログを記録する

フローがサービス境界をまたがる関連付け ID を含めます。 トランザクション フローが複数のサービスを経由し、そのいずれかが失敗した場合、関連付け ID は、アプリケーション全体で要求を追跡し、トランザクションが失敗した理由を特定するのに役立ちます。

### <a name="use-semantic-structured-logging"></a>セマンティックの (構造化された) ログ記録を使用する

構造化されたログを使用すると、ログ データの使用と分析の自動化が容易になり、クラウド スケールではそれが特に重要です。 一般に、Azure リソースのメトリックと診断データを格納するには、ストレージ アカウントではなく、Log Analytics ワークスペースをお勧めします。 これにより、Kusto クエリを使って、必要なデータを迅速に構造化された形式で取得できます。 また、Azure Monitor API と Azure Log Analytics API を使うこともできます。

### <a name="use-asynchronous-logging"></a>非同期のログ記録を使用する

同期ログ記録操作では、アプリケーションのコードがブロックされることがあり、ログが書き込まれると、バックアップが要求されます。 アプリケーションのログ記録の間に可用性を維持するには、非同期のログ記録を使います。

### <a name="separate-application-logging-from-auditing"></a>アプリケーションのログと監査を分ける

一般に、監査レコードはコンプライアンスまたは法的要件のために保持され、完全なものである必要があります。 トランザクションが破棄されるのを避けるには、診断ログとは別に監査ログを維持します。

## <a name="remote-call-statistics"></a>リモート呼び出しの統計情報

リモート呼び出しの統計情報をリアルタイムで追跡してレポートし、この情報を確認する簡単な方法を用意して、運用チームがアプリケーションの正常性を即座に確認できるようにします。 待機時間、スループット、および 99 および 95 パーセンタイルのエラーなど、リモート呼び出しのメトリックをまとめます。

## <a name="transient-exceptions-and-retries"></a>一時的な例外と再試行

時間をかけて一時的な例外と再試行の数を追跡し、アプリケーションの再試行ロジックにおけるエラーや障害を明らかにします。 時間とともに例外が増加する傾向は、サービスに問題があり、失敗する可能性があることを示している場合があります。 詳細については、「 [再試行サービス固有のガイダンス](../best-practices/retry-service-specific.md)」を参照してください。

## <a name="early-warning-system"></a>早期警告システム

アプリケーションで、積極的な介入が必要な可能性がある警告の兆候を監視します。 アプリケーションの全体的な正常性とその依存関係を評価するツールは、システムまたはそのコンポーネントが突然使用できなくなったことを迅速に認識するのに役立ちます。 それらを使って、早期警告システムを実装します。

1. 一時的な例外やリモート呼び出しの待機時間など、アプリケーションの正常性の主要なパフォーマンス指標を明らかにします。
1. 問題が重大になり、復旧対応が必要になる前に、問題が特定されるレベルで、しきい値を設定します。
1. しきい値に達したときに、操作にアラートを送信します。

[Microsoft System Center 2016](https://www.microsoft.com/cloud-platform/system-center) またはサード パーティ製のツールを使って、監視機能を提供することを検討します。 ほとんどの監視ソリューションは、主要なパフォーマンス カウンターとサービスの可用性を追跡します。 [Azure Resource Health](/azure/service-health/resource-health-checks-resource-types) では組み込みの正常性状態チェックがいくつか提供されており、Azure サービスの調整の診断に役立ちます。

## <a name="subscription-and-service-limitations"></a>サブスクリプションとサービスの制限

Azure サブスクリプションには、リソース グループの数、コアの数、ストレージ アカウントの数など、特定のリソースの種類に対する制限があります。 アプリケーションが Azure サブスクリプションの制限に達しないようにするには、制限やクォータに近づいているサービスをポーリングするアラートを作成します。

次のサブスクリプション制限にアラートで対処します。

### <a name="individual-services"></a>個別サービス

個々の Azure サービスには、ストレージ、スループット、接続数、秒あたりの要求数、その他のメトリックに関して、消費量に制限があります。 これらの制限を超えてリソースを使おうとするとアプリケーションは失敗し、サービスの調整が行われて、ダウンタイムが発生する可能性があります。

特定のサービスおよびアプリケーションの要件に応じて、多くの場合、スケールアップ (たとえば、別の価格レベルの選択) またはスケールアウト (新しいインスタンスの追加) により、これらの制限を超えないようにすることができます。

### <a name="azure-storage-scalability-and-performance-targets"></a>Azure Storage のスケーラビリティおよびパフォーマンスのターゲット

Azure には、サブスクリプションごとに許可されるストレージ アカウントの最大数があります。 現在のサブスクリプションで使用可能な数より多くのストレージ アカウントがアプリケーションで必要な場合は、新しいサブスクリプションを作成してストレージ アカウントを追加する必要があります。 詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](/azure/azure-subscription-service-limits/#storage-limits)」をご覧ください。

Azure Storage のスケーラビリティとパフォーマンスのターゲットを超える場合は、アプリケーションでストレージが調整されます。 詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](/azure/storage/storage-scalability-targets/)」を参照してください。

### <a name="scalability-targets-for-virtual-machine-disks"></a>仮想マシンのディスクのスケーラビリティ ターゲット

Azure のサービスとしてのインフラストラクチャ (IaaS) VM では、VM のサイズやストレージ アカウントの種類など、いくつかの要因に応じた数のデータ ディスクの接続がサポートされています。 アプリケーションが仮想マシンのディスクのスケーラビリティのターゲットを超える場合は、追加のストレージ アカウントをプロビジョニングし、そこに仮想マシンのディスクを作成してください。 詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](/azure/storage/storage-scalability-targets/#scalability-targets-for-virtual-machine-disks)」を参照してください。

### <a name="vm-size"></a>VM サイズ

VM の実際の CPU、メモリ、ディスク、I/O が VM サイズの制限に近づいている場合、アプリケーションでキャパシティの問題が発生する可能性があります。 問題を修正するには、VM のサイズを増やします。 VM のサイズについては、「[Azure の Windows 仮想マシンのサイズ](/azure/virtual-machines/virtual-machines-windows-sizes/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。

時間と共にワークロードが変動する場合は、仮想マシン スケール セットを使って VM インスタンスの数を自動的にスケーリングすることを検討します。 そうしないと、VM の数を手動で増減することが必要になります。 詳細については、[仮想マシン スケール セットの概要](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview/)に関する記事を参照してください。

### <a name="azure-sql-database"></a>Azure SQL Database

アプリケーションのデータベース トランザクション ユニット (DTU) の要件を処理するのに、Azure SQL Database のレベルが適切ではない場合、データの使用が調整されます。 適切なサービス プランの選択の詳細については、「[Azure SQL Database の購入モデル](/azure/sql-database/sql-database-service-tiers/)」を参照してください。

## <a name="third-party-services"></a>サード パーティーのサービス

アプリケーションがサード パーティのサービスに依存している場合は、それらのサービスがどのように失敗する可能性があるか、およびアプリケーションに対する障害の影響を明らかにします。

サード パーティのサービスには、監視と診断が含まれない可能性があります。 そのようなサービスに対する呼び出しをログに記録し、一意の識別子を使って、それらをアプリケーションの正常性と診断のログに関連付けます。 監視と診断の実証された方法については、[監査と診断のガイダンス](../best-practices/monitoring.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ディザスター リカバリーを計画する](./disaster-recovery.md)
