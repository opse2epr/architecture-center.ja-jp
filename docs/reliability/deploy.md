---
title: 回復性と可用性のための Azure アプリケーションのデプロイ
description: Azure で信頼性の高いデプロイを作成するための手法の概要
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: d8df3fe1c3353c60462959a625ba13ae47b96cf8
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59642876"
---
# <a name="deploying-azure-applications-for-resiliency-and-availability"></a>回復性と可用性のための Azure アプリケーションのデプロイ

Azure リソース、アプリケーション コード、および構成設定をプロビジョニングして更新する際は、反復可能で予測可能なプロセスがエラーとダウンタイムの回避に役立ちます。 オンデマンドで実行し、エラーが発生した場合には再実行できる自動プロセスでデプロイすることをお勧めします。 円滑に実行されたデプロイ プロセスは、プロセスのドキュメントにその方法を保存することができます。

## <a name="automate-processes"></a>プロセスを自動化する

リソースをオンデマンドでアクティブ化し、ソリューションをすばやくデプロイし、人的エラーを最小限に抑え、一貫性があり反復可能な結果を生成するには、デプロイと更新を自動化してください。

### <a name="automate-as-many-processes-as-possible"></a>可能な限り多くのプロセスを自動化する

最も信頼性の高いデプロイ プロセスが自動化され、*べき等*&mdash;になります。つまり同じ結果を繰り返し生成できます。

- Azure リソースのプロビジョニングを自動化するために、[Terraform](/azure/virtual-machines/windows/infrastructure-automation#terraform)、[Ansible](/azure/virtual-machines/windows/infrastructure-automation#ansible)、[Chef](/azure/virtual-machines/windows/infrastructure-automation#chef)、[Puppet](/azure/virtual-machines/windows/infrastructure-automation#puppet)、[Azure PowerShell](/powershell/azure/overview)、 [Azure コマンドライン インターフェイス (CLI)](/cli/azure)、または  [Azure Resource Manager テンプレート](/azure/azure-resource-manager/resource-group-overview#template-deployment)を使用できます。
- VM を構成するには、 [cloud-init](/azure/virtual-machines/windows/infrastructure-automation#cloud-init)  (Linux VM の場合)、または  [Azure Automation State Configuration](/azure/automation/automation-dsc-overview) (DSC) を使用できます。
- アプリケーションのデプロイを自動化するには、 [Azure DevOps Services](/azure/virtual-machines/windows/infrastructure-automation#azure-devops-services)、 [Jenkins](/azure/virtual-machines/windows/infrastructure-automation#jenkins)、またはその他の CI/CD ソリューションを使用できます。

ベスト プラクティスとして、パラメーターとスクリプトの使用例の説明が記載され、すばやくアクセスできる、カテゴリ化された自動化スクリプトのリポジトリを作成します。 このドキュメントが自分の Azure デプロイと常に同期されるようにし、リポジトリを管理するプライマリ ユーザーを指定します。

自動化スクリプトを使用すると、ディザスター リカバリーのためにオンデマンドでリソースをアクティブ化することもできます。

### <a name="use-code-to-provision-and-configure-infrastructure"></a>プロビジョニングするコードの使用とインフラストラクチャの構成

この*コードとしてのインフラストラクチャ*と呼ばれる演習には、宣言型の方法、命令型の方法 (またはその両方の組み合わせ) を使用することができます。

- 宣言型の方法の例として、[Resource Manager テンプレート](/azure/azure-resource-manager/resource-group-overview#template-deployment)があります。
- [PowerShell](/powershell/azure/overview) スクリプトは、命令型の方法の一例です。

### <a name="practice-immutable-infrastructure"></a>イミュータブル インフラストラクチャを実践する

つまり、運用環境にデプロイされた後はインフラストラクチャを変更しないでください。 アドホックの変更が適用された後、正確な変更内容がわからないと、システムのトラブルシューティングが難しいことがあります。

### <a name="automate-and-test-deployment-and-maintenance-tasks"></a>デプロイおよび保守のタスクを自動化してテストする

分散アプリケーションは、連携して動作する必要がある、複数の部分で構成されます。 デプロイでは、スクリプトのような実証済みのメカニズムを利用して構成の更新と検証を行ったり、デプロイ プロセスを自動化したりできます。 エラーによってさらにダウンタイムが確実に発生しないようにするため、すべてのプロセスを完全にテストしてください。

### <a name="implement-deployment-security-measures"></a>デプロイのセキュリティ対策を実装する

すべてのデプロイ ツールに、デプロイされたアプリケーションを保護するセキュリティ制限を組み込む必要があります。 デプロイ ポリシーを慎重に定義して適用し、人間の介入の必要性を最小限に抑えます。

## <a name="deploy-to-multiple-regions-and-instances"></a>複数のリージョンとインスタンスにデプロイする

サービスの 1 つのインスタンスに依存するアプリケーションでは、単一障害点が発生します。 回復性とスケーラビリティを改善するには、複数のインスタンスをプロビジョニングします。

- [Azure App Service](/azure/app-service/app-service-value-prop-what-is/) の場合は、複数のインスタンスを提供する [App Service プラン](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview/)を選択してください。
- [Azure Cloud Services](/azure/cloud-services/cloud-services-choose-me) の場合は、[複数インスタンス](/azure/cloud-services/cloud-services-choose-me/#scaling-and-management)を使用するように各ロールを構成してください。
- [Azure Virtual Machines](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) の場合は、確実にアーキテクチャに 1 つ以上の VM が含まれ、[可用性セット](/azure/virtual-machines/virtual-machines-windows-manage-availability/)に各 VM が含まれるようにします。

### <a name="consider-deploying-across-multiple-regions"></a>複数のリージョンにわたるデプロイを検討する

少なくともクリティカルなアプリケーションとアプリケーションのサービスを複数のリージョンにデプロイすることをお勧めします。 アプリケーションが 1 つのリージョンにデプロイされている場合は、まれにリージョン全体が使用できなくなった場合に、アプリケーションも使用できなくなります。

1 つのリージョンにデプロイする場合は、予期しない障害への対応として、セカンダリ リージョンへの再デプロイの準備を検討してください。

### <a name="redeploy-to-a-secondary-region"></a>セカンダリ リージョンに再デプロイする

レプリケーションのない 1 つのプライマリ リージョンでアプリケーションとデータベースを実行すると、復旧戦略により別のリージョンに再デプロイされることがあります。 このソリューションは手頃な価格ですが、最も適しているのは長い復旧時間を許容できる、クリティカルでないアプリケーションです。

この戦略を選択する場合は、可能な限り、再デプロイ プロセスを自動化し、障害対応のテストに再デプロイのシナリオを含めます。

再デプロイ プロセスを自動化する場合は、[Azure Site Recovery](/azure/site-recovery/) の使用を検討してください。

## <a name="use-staging-and-production-environments"></a>ステージングと運用環境を使用する

ステージングと運用環境を適切に使用することで、高度に制御された方法で更新プログラムを運用環境にプッシュし、予期しないデプロイの問題による中断を最小限に抑えることができます。

- [*Blue/Green デプロイ*](https://martinfowler.com/bliki/BlueGreenDeployment.html) では、ライブ アプリケーションとは別の運用環境に更新プログラムをデプロイする必要があります。 デプロイの検証が完了したら、トラフィック ルーティングを更新されたバージョンに切り替えます。 これを実行する方法の 1 つが、Azure App Service で使用できる[ステージング スロット](/azure/app-service/web-sites-staged-publishing)を使用して、運用環境に移動する前にデプロイをステージングすることです。
- [*カナリア リリース*](https://martinfowler.com/bliki/CanaryRelease.html) は、Blue/Green デプロイと似ています。 すべてのトラフィックを更新されたアプリケーションに切り替える代わりに、トラフィックのごく一部のみを新しいデプロイにルーティングします。 問題が発生した場合は、以前のデプロイに戻します。 そうでない場合は、より多くのトラフィックを段階的に新しいバージョンにルーティングします。 Azure App Service を使用している場合は、運用環境でのテスト機能を使用してカナリア リリースを管理することができます。

## <a name="create-a-rollback-plan-for-deployment-and-updates"></a>デプロイと更新のためのロールバック計画を作成する

デプロイに失敗した場合、アプリケーションが使用できなくなる可能性があります。 ダウンタイムを最小限に抑えるため、直前の正常なバージョンに戻すためのロールバック プロセスを設計します。 アプリが依存しているデータベースとその他のサービスの変更をロールバックする戦略を含めます。

Azure App Service を使用している場合は、直前の正常なサイト スロットを設定し、これを使用して Web または API アプリのデプロイからロールバックできます。

## <a name="log-and-audit-deployments"></a>デプロイのログを記録して監査する

できるだけ多くのバージョン固有の情報を把握するため、確実なログ記録の方法を実装してください。 ステージング デプロイ手法を使用する場合、運用環境では複数のバージョンのアプリケーションが実行されることになります。 問題が発生した場合は、どのバージョンが原因かを決定します。

## <a name="document-release-processes"></a>リリース プロセスのドキュメント

詳細なリリース プロセスのドキュメントがなければ、オペレーターが不適切な更新プログラムやアプリケーションに不適切な構成設定をデプロイする可能性があります。 リリース プロセスを明確に定義して文書化し、オペレーション チーム全体が確実に利用できるようにしてください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [信頼性に対するアプリケーションの正常性を監視する](./monitoring.md)
