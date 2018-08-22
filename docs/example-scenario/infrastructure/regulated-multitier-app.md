---
title: 規制対象業界向けのセキュリティで保護された Windows Web アプリケーション
description: スケール セット、Application Gateway、ロード バランサーを使用する、セキュリティで保護された多層 Web アプリケーションを、Azure 上の Windows Server を使用して構築するための実証済みのシナリオ。
author: iainfoulds
ms.date: 07/11/2018
ms.openlocfilehash: aba714fc1955341645d0faa400768bc09fb8e50b
ms.sourcegitcommit: 71cbef121c40ef36e2d6e3a088cb85c4260599b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060993"
---
# <a name="secure-windows-web-application-for-regulated-industries"></a><span data-ttu-id="2c03a-103">規制対象業界向けのセキュリティで保護された Windows Web アプリケーション</span><span class="sxs-lookup"><span data-stu-id="2c03a-103">Secure Windows web application for regulated industries</span></span>

<span data-ttu-id="2c03a-104">このサンプル シナリオは、多層アプリケーションをセキュリティで保護する必要がある規制対象業界に適用されます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-104">This sample scenario is applicable to regulated industries that have a need to secure multi-tier applications.</span></span> <span data-ttu-id="2c03a-105">このシナリオでは、フロントエンド ASP.NET アプリケーションから、保護されたバックエンド Microsoft SQL Server クラスターに安全に接続します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-105">In this scenario, a front-end ASP.NET application securely connects to a protected back-end Microsoft SQL Server cluster.</span></span>

<span data-ttu-id="2c03a-106">アプリケーションのサンプル シナリオには、手術室アプリケーションの実行、患者の予約とレコードの保存、または処方箋の差し替えと注文が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-106">Example application scenarios include running operating room applications, patient appointments and records keeping, or prescription refills and ordering.</span></span> <span data-ttu-id="2c03a-107">これまでは、これらのシナリオのために、従来のオンプレミス アプリケーションとサービスを組織が維持する必要がありました。</span><span class="sxs-lookup"><span data-stu-id="2c03a-107">Traditionally, organizations had to maintain legacy on-premises applications and services for these scenarios.</span></span> <span data-ttu-id="2c03a-108">これらの Windows Server アプリケーションを、セキュリティで保護されたスケーラブルな方法で Azure にデプロイすることにより、組織は自身のデプロイを最新化し、オンプレミスの運用コストと管理オーバーヘッドを減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-108">With a secure way and scalable way to deploy these Windows Server applications in Azure, organizations can modernize their deployments are reduce their on-premises operating costs and management overhead.</span></span>

## <a name="related-use-cases"></a><span data-ttu-id="2c03a-109">関連するユース ケース</span><span class="sxs-lookup"><span data-stu-id="2c03a-109">Related use cases</span></span>

<span data-ttu-id="2c03a-110">次のユース ケースについて、このシナリオを検討してください。</span><span class="sxs-lookup"><span data-stu-id="2c03a-110">Consider this scenario for the following use cases:</span></span>

* <span data-ttu-id="2c03a-111">セキュリティで保護されたクラウド環境におけるアプリケーション デプロイの最新化。</span><span class="sxs-lookup"><span data-stu-id="2c03a-111">Modernizing application deployments in a secure cloud environment.</span></span>
* <span data-ttu-id="2c03a-112">従来のオンプレミス アプリケーションとサービス管理の軽減。</span><span class="sxs-lookup"><span data-stu-id="2c03a-112">Reducing legacy on-premises application and service management.</span></span>
* <span data-ttu-id="2c03a-113">新しいアプリケーション プラットフォームでの医療と患者体験の向上。</span><span class="sxs-lookup"><span data-stu-id="2c03a-113">Improving patient healthcare and experience with new application platforms.</span></span>

## <a name="architecture"></a><span data-ttu-id="2c03a-114">アーキテクチャ</span><span class="sxs-lookup"><span data-stu-id="2c03a-114">Architecture</span></span>

![規制対象業界向けの多層 Windows Server アプリケーションに関与する Azure コンポーネントのアーキテクチャ概要][architecture]

<span data-ttu-id="2c03a-116">このシナリオでは、ASP.NET および Microsoft SQL Server を使用する規制対象業界の多層アプリケーションに対応できます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-116">This scenario covers a multi-tier regulated industries application that uses ASP.NET and Microsoft SQL Server.</span></span> <span data-ttu-id="2c03a-117">このシナリオのデータ フローは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2c03a-117">The data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="2c03a-118">ユーザーが Azure Application Gateway 経由で、フロントエンドの規制対象業界向け ASP.NET アプリケーションにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="2c03a-118">Users access the front-end ASP.NET regulated industries application through an Azure Application Gateway.</span></span>
2. <span data-ttu-id="2c03a-119">Application Gateway は、Azure 仮想マシン スケール セット内でトラフィックを VM インスタンスに分散します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-119">The Application Gateway distributes traffic to VM instances within an Azure virtual machine scale set.</span></span>
3. <span data-ttu-id="2c03a-120">ASP.NET アプリケーションは、Azure Load Balancer を使用して、バックエンド層の Microsoft SQL Server クラスターに接続します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-120">The ASP.NET application connects to Microsoft SQL Server cluster in a back-end tier via an Azure load balancer.</span></span> <span data-ttu-id="2c03a-121">これらのバックエンド SQL Server インスタンスは別個の Azure 仮想ネットワークにあり、トラフィック フローを制限するネットワーク セキュリティ グループの規則によってセキュリティで保護されています。</span><span class="sxs-lookup"><span data-stu-id="2c03a-121">These backend SQL Server instances are in a separate Azure virtual network, secured by network security group rules that limit traffic flow.</span></span>
4. <span data-ttu-id="2c03a-122">ロード バランサーは、SQL Server のトラフィックを、別の仮想マシン スケール セット内の VM インスタンスに分散します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-122">The load balancer distributes SQL Server traffic to VM instances in another virtual machine scale set.</span></span>
5. <span data-ttu-id="2c03a-123">Azure Blob Storage は、バックエンド層の SQL Server クラスター用のクラウド監視として機能します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-123">Azure Blob Storage acts as a Cloud Witness for the SQL Server cluster in the backend tier.</span></span>  <span data-ttu-id="2c03a-124">VNet 内からの接続は、Azure Storage の VNet サービス エンドポイントで有効にされます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-124">The connection from within the VNet is enabled with a VNet Service Endpoint for Azure Storage.</span></span>

### <a name="components"></a><span data-ttu-id="2c03a-125">コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2c03a-125">Components</span></span>

* <span data-ttu-id="2c03a-126">[Azure Application Gateway][appgateway-docs] は、アプリケーション対応のレイヤー 7 Web トラフィック ロード バランサーで、特定のルーティング規則に基づいてトラフィックを分散できます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-126">[Azure Application Gateway][appgateway-docs] is a layer 7 web traffic load balancer that is application-aware and can distribute traffic based on specific routing rules.</span></span> <span data-ttu-id="2c03a-127">App Gateway で、SSL オフロードを処理し、Web サーバーのパフォーマンスを向上させることもできます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-127">App Gateway can also handle SSL offloading for improved web server performance.</span></span>
* <span data-ttu-id="2c03a-128">[Azure Virtual Network][vnet-docs] を使用すると、VM などのリソースが、他の Azure リソース、インターネット、およびオンプレミスのネットワークと安全に通信することができます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-128">[Azure Virtual Network][vnet-docs] allows resources such as VMs to securely communicate with each other, the Internet, and on-premises networks.</span></span> <span data-ttu-id="2c03a-129">仮想ネットワークにより、分離性、セグメント化、トラフィックのフィルター処理とルーティングが提供され、場所間の接続が可能になります。</span><span class="sxs-lookup"><span data-stu-id="2c03a-129">Virtual networks provide isolation and segmentation, filter and route traffic, and allow connection between locations.</span></span> <span data-ttu-id="2c03a-130">このシナリオでは適切な NSG で結合されている 2 つ仮想ネットワークを使用して、[非武装地帯][dmz] (DMZ) と、アプリケーション コンポーネントの分離性が提供されます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-130">Two virtual networks combined with the appropriate NSGs are used in this scenario to provide a [demilitarized zone][dmz] (DMZ) and isolation of the application components.</span></span> <span data-ttu-id="2c03a-131">仮想ネットワーク ピアリングによって、2 つのネットワークが接続されます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-131">Virtual network peering connects the two networks together.</span></span>
* <span data-ttu-id="2c03a-132">[Azure 仮想マシン スケール セット][scaleset-docs]では、負荷分散が行われる同一の VM のグループを作成して管理できます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-132">[Azure virtual machine scale set][scaleset-docs] let you create and manager a group of identical, load balanced, VMs.</span></span> <span data-ttu-id="2c03a-133">需要または定義されたスケジュールに応じて、VM インスタンスの数を自動的に増減させることができます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-133">The number of VM instances can automatically increase or decrease in response to demand or a defined schedule.</span></span> <span data-ttu-id="2c03a-134">このシナリオでは 2 つの別個の仮想マシン スケール セットが使用されます。1 つはフロント エンドの ASP.NET アプリケーション インスタンス用、もう 1 つはバックエンドの SQL Server クラスター VM インスタンス用です。</span><span class="sxs-lookup"><span data-stu-id="2c03a-134">Two separate virtual machine scale sets are used in this scenario - one for the frontend ASP.NET application instances, and one for the backend SQL Server cluster VM instances.</span></span> <span data-ttu-id="2c03a-135">PowerShell の Desired State Configuration (DSC) または Azure カスタム スクリプト拡張機能を使用すると、必要なソフトウェアおよび構成設定で VM インスタンスをプロビジョニングできます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-135">PowerShell desired state configuration (DSC) or the Azure custom script extension can be used to provision the VM instances with the required software and configuration settings.</span></span>
* <span data-ttu-id="2c03a-136">[Azure ネットワーク セキュリティ グループ][nsg-docs]には、ソースまたはターゲット IP アドレス、ポート、およびプロトコルを基に、受信/送信ネットワーク トラフィックを許可または拒否するセキュリティ規則の一覧が含まれています。</span><span class="sxs-lookup"><span data-stu-id="2c03a-136">[Azure network security groups][nsg-docs] contains a list of security rules that allow or deny inbound or outbound network traffic based on source or destination IP address, port, and protocol.</span></span> <span data-ttu-id="2c03a-137">このシナリオの仮想ネットワークは、アプリケーション コンポーネント間のトラフィック フローを制限するネットワーク セキュリティ グループ規則によって保護されています。</span><span class="sxs-lookup"><span data-stu-id="2c03a-137">The virtual networks in this scenario are secured with network security group rules that restrict the flow of traffic between the application components.</span></span>
* <span data-ttu-id="2c03a-138">[Azure Load Balancer][loadbalancer-docs] は、規則と正常性プローブに従って受信トラフィックを分散します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-138">[Azure load balancer][loadbalancer-docs] distributes inbound traffic according to rules and health probes.</span></span> <span data-ttu-id="2c03a-139">ロード バランサーは、低遅延と高スループットを実現できるだけでなく、あらゆる TCP アプリケーションと UDP アプリケーションの数百万ものフローにスケールアップできます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-139">A load balancer provides low latency and high throughput, and scales up to millions of flows for all TCP and UDP applications.</span></span> <span data-ttu-id="2c03a-140">このシナリオでは内部ロード バランサーを使用して、フロントエンド アプリケーション層からバックエンド SQL Server クラスターへのトラフィックを分散します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-140">An internal load balancer is used in this scenario to distribute traffic from the frontend application tier to the backend SQL Server cluster.</span></span>
* <span data-ttu-id="2c03a-141">[Azure Blob Storage][cloudwitness-docs] は、SQL Server クラスター用のクラウド監視の場所として機能します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-141">[Azure Blob Storage][cloudwitness-docs] acts a Cloud Witness location for the SQL Server cluster.</span></span> <span data-ttu-id="2c03a-142">この監視は、クォーラムの決定に追加の投票が必要な、クラスター操作と意思決定で使用されます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-142">This witness is used for cluster operations and decisions that require an additional vote to decide quorum.</span></span> <span data-ttu-id="2c03a-143">クラウド監視を使用すると、従来のファイル共有監視として機能する追加の VM が不要になります。</span><span class="sxs-lookup"><span data-stu-id="2c03a-143">Using Cloud Witness removes the need for an additional VM to act as a traditional File Share Witness.</span></span>

### <a name="alternatives"></a><span data-ttu-id="2c03a-144">代替手段</span><span class="sxs-lookup"><span data-stu-id="2c03a-144">Alternatives</span></span>

* <span data-ttu-id="2c03a-145">インフラストラクチャには OS に依存しているものがないため、\*nix、Windows は、他のさまざまな OS で置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-145">\*nix, windows can easily be replaced by a variety of other OS's as nothing in the infrastructure depends on the OS.</span></span>

* <span data-ttu-id="2c03a-146">バックエンド データ ストアの代わりに、[Linux 用 SQL Server][sql-linux] を使用できます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-146">[SQL Server for Linux][sql-linux] can replace the back-end data store.</span></span>

* <span data-ttu-id="2c03a-147">データ ストアの代わりに、[Cosmos DB][cosmos] を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-147">[Cosmos DB][cosmos] is another another alternative for the data store.</span></span>

## <a name="considerations"></a><span data-ttu-id="2c03a-148">考慮事項</span><span class="sxs-lookup"><span data-stu-id="2c03a-148">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="2c03a-149">可用性</span><span class="sxs-lookup"><span data-stu-id="2c03a-149">Availability</span></span>

<span data-ttu-id="2c03a-150">このシナリオの VM インスタンスは、可用性ゾーンにまたがってデプロイされます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-150">The VM instances in this scenario are deployed across Availability Zones.</span></span> <span data-ttu-id="2c03a-151">それぞれのゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。</span><span class="sxs-lookup"><span data-stu-id="2c03a-151">Each zone is made up of one or more datacenters equipped with independent power, cooling, and networking.</span></span> <span data-ttu-id="2c03a-152">最低で 3 つのゾーンを、有効なすべてのリージョンで使用できます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-152">A minimum of three zones are available in all enabled regions.</span></span> <span data-ttu-id="2c03a-153">このようにゾーン間で VM インスタンスを分散させることにより、アプリケーション層に高可用性が実現します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-153">This distribution of VM instances across zones provides high availability to the application tiers.</span></span> <span data-ttu-id="2c03a-154">詳細については、[Azure の可用性ゾーンの概要][azureaz-docs]に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2c03a-154">For more information, see [what are Availability Zones in Azure?][azureaz-docs]</span></span>

<span data-ttu-id="2c03a-155">データベース層は、AlwaysOn 可用性グループを使用するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-155">The database tier can be configured to use Always On availability groups.</span></span> <span data-ttu-id="2c03a-156">この SQL Server 構成により、クラスター内の 1 つのプライマリ データベースが、最大 8 つのセカンダリ データベースと共に構成されます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-156">With this SQL Server configuration, one primary database within a cluster is configured with up to eight secondary databases.</span></span> <span data-ttu-id="2c03a-157">プライマリ データベースで問題が発生した場合、クラスターは、セカンダリ データベースのいずれかにフェールオーバーします。これにより、アプリケーションを引き続き使用できます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-157">If an issue occurs with the primary database, the cluster fails over to one of the secondary databases, which allows the application to continue to be available.</span></span> <span data-ttu-id="2c03a-158">詳細については、[SQL Server 用の Always On 可用性グループの概要][sqlalwayson-docs]に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2c03a-158">For more information, see [Overview of Always On availability groups for SQL Server][sqlalwayson-docs].</span></span>

<span data-ttu-id="2c03a-159">可用性に関する他のトピックについては、Azure アーキテクチャ センターの[可用性のチェックリスト][availability]を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2c03a-159">For other availability topics, see the [availability checklist][availability] in the Azure Architecure Center.</span></span>

### <a name="scalability"></a><span data-ttu-id="2c03a-160">スケーラビリティ</span><span class="sxs-lookup"><span data-stu-id="2c03a-160">Scalability</span></span>

<span data-ttu-id="2c03a-161">このシナリオでは、フロントエンド コンポーネントとバックエンド コンポーネントに対して、仮想マシン スケール セットを使用します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-161">This scenario uses virtual machine scale sets for the frontend and backend components.</span></span> <span data-ttu-id="2c03a-162">スケール セットにより、顧客の要求に応じて、または定義されているスケジュールに基づいて、フロントエンド アプリケーション層を実行する VM インスタンスの数を自動的にスケーリングできます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-162">With scale sets, the number of VM instances that run the frontend application tier can automatically scale in response to customer demand, or based on a defined schedule.</span></span> <span data-ttu-id="2c03a-163">詳細については、[仮想マシン スケール セットでの自動スケールの概要][vmssautoscale-docs]に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2c03a-163">For more information, see [Overview of autoscale with virtual machine scale sets][vmssautoscale-docs].</span></span>

<span data-ttu-id="2c03a-164">スケーラビリティに関する他のトピックについては、Azure アーキテクチャ センターの[スケーラビリティのチェックリスト][scalability]を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2c03a-164">For other scalability topics, see the [scalability checklist][scalability] in the Azure Architecure Center.</span></span>

### <a name="security"></a><span data-ttu-id="2c03a-165">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="2c03a-165">Security</span></span>

<span data-ttu-id="2c03a-166">フロントエンド アプリケーション層へのすべての仮想ネットワーク トラフィックが、ネットワーク セキュリティ グループによって保護されます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-166">All the virtual network traffic into the frontend application tier and protected by network security groups.</span></span> <span data-ttu-id="2c03a-167">フロントエンド アプリケーション層 VM インスタンスのみがバックエンド データベース層にアクセスできるように、規則によってトラフィックのフローが制限されます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-167">Rules limit the flow of traffic so that only the frontend application tier VM instances can access the backend database tier.</span></span> <span data-ttu-id="2c03a-168">データベース層からの送信インターネット トラフィックは許可されません。</span><span class="sxs-lookup"><span data-stu-id="2c03a-168">No outbound Internet traffic is allowed from the database tier.</span></span> <span data-ttu-id="2c03a-169">攻撃フットプリントを減らすために、ダイレクト リモート管理ポートは開かれていません。</span><span class="sxs-lookup"><span data-stu-id="2c03a-169">To reduce the attack footprint, no direct remote management ports are open.</span></span> <span data-ttu-id="2c03a-170">詳細については、[Azure ネットワーク セキュリティ グループ][nsg-docs]に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2c03a-170">For more information, see [Azure network security groups][nsg-docs].</span></span>

<span data-ttu-id="2c03a-171">Payment Card Industry Data Security Standards (PCI DSS 3.2) の展開のガイダンスについては、[コンプライアンス インフラストラクチャ][pci-dss]に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2c03a-171">To view guidance on deploying Payment Card Industry Data Security Standards (PCI DSS 3.2) [compliant infrastructure][pci-dss].</span></span> <span data-ttu-id="2c03a-172">セキュリティで保護されたシナリオの設計に関する一般的なガイダンスについては、「[Azure のセキュリティのドキュメント][security]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2c03a-172">For general guidance on designing secure scenarios, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="2c03a-173">回復性</span><span class="sxs-lookup"><span data-stu-id="2c03a-173">Resiliency</span></span>

<span data-ttu-id="2c03a-174">このシナリオでは、可用性ゾーンおよび仮想マシン スケール セットと組み合わて、Azure Application Gateway とロード バランサーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-174">In combination with the use of Availability Zones and virtual machine scale sets, this scenario uses Azure Application Gateway and load balancer.</span></span> <span data-ttu-id="2c03a-175">これら 2 つのネットワーク コンポーネントは、接続されている VM インスタンスにトラフィックを分散します。また、コンポーネントには正常性プローブが含まれ、正常な状態の VM にのみトラフィックが分散されることが保証されます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-175">These two networking components distribute traffic to the connected VM instances, and include health probes that ensure traffic is only distributed to healthy VMs.</span></span> <span data-ttu-id="2c03a-176">2 つの Application Gateway インスタンスは、アクティブ/パッシブ構成で構成され、ゾーン冗長ロード バランサーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-176">Two Application Gateway instances are configured in an active-passive configuration, and a zone-redundant load balancer is used.</span></span> <span data-ttu-id="2c03a-177">トラフィックを中断し、エンドユーザーへのアクセスに影響を及ぼす可能性のある問題に対する回復性が、この構成によってネットワーク リソースとアプリケーションで実現します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-177">This configuration makes the networking resources and application resilient to issues that would otherwise disrupt traffic and impact end-user access.</span></span>

<span data-ttu-id="2c03a-178">回復性に優れたシナリオの設計に関する一般的なガイダンスについては、「[回復性に優れた Azure 用アプリケーションの設計][resiliency]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2c03a-178">For general guidance on designing resilient scenarios, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="2c03a-179">シナリオのデプロイ</span><span class="sxs-lookup"><span data-stu-id="2c03a-179">Deploy the scenario</span></span>

<span data-ttu-id="2c03a-180">**前提条件。**</span><span class="sxs-lookup"><span data-stu-id="2c03a-180">**Prerequisites.**</span></span>

* <span data-ttu-id="2c03a-181">既存の Azure アカウントが必要です。</span><span class="sxs-lookup"><span data-stu-id="2c03a-181">You must have an existing Azure account.</span></span> <span data-ttu-id="2c03a-182">Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。</span><span class="sxs-lookup"><span data-stu-id="2c03a-182">If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.</span></span>
* <span data-ttu-id="2c03a-183">バックエンド スケール セットに SQL Server クラスターをデプロイするには、Active Directory ディレクトリ サービス ドメインが必要です。</span><span class="sxs-lookup"><span data-stu-id="2c03a-183">To deploy a SQL Server cluster into the backend scale set, you would need an Active Directory Directory Services domain.</span></span>

<span data-ttu-id="2c03a-184">Azure Resource Manager テンプレートを使用して、このシナリオのコア インフラストラクチャをデプロイするには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-184">To deploy the core infrastructure for this scenario with an Azure Resource Manager template, perform the following steps.</span></span>

1. <span data-ttu-id="2c03a-185">**[Deploy to Azure]\(Azure にデプロイ\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-185">Select the **Deploy to Azure** button:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Finfrastructure%2Fregulated-multitier-app%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
2. <span data-ttu-id="2c03a-186">Azure portal でテンプレートのデプロイが開くまで待ってから、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-186">Wait for the template deployment to open in the Azure portal, then complete the following steps:</span></span>
   * <span data-ttu-id="2c03a-187">リソース グループの **[新規作成]** を選択し、テキスト ボックスに名前 (例: *myWindowsscenario*) を入力します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-187">Choose to **Create new** resource group, then provide a name such as *myWindowsscenario* in the text box.</span></span>
   * <span data-ttu-id="2c03a-188">**[場所]** ドロップダウン ボックスでリージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-188">Select a region from the **Location** drop-down box.</span></span>
   * <span data-ttu-id="2c03a-189">仮想マシン スケール セット インスタンスのユーザー名と安全なパスワードを入力します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-189">Provide a username and secure password for the virtual machine scale set instances.</span></span>
   * <span data-ttu-id="2c03a-190">使用条件を確認し、**[上記の使用条件に同意する]** をオンにします。</span><span class="sxs-lookup"><span data-stu-id="2c03a-190">Review the terms and conditions, then check **I agree to the terms and conditions stated above**.</span></span>
   * <span data-ttu-id="2c03a-191">**[購入]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2c03a-191">Select the **Purchase** button.</span></span>

<span data-ttu-id="2c03a-192">デプロイが完了するまでに 15 から 20 分かかることがあります。</span><span class="sxs-lookup"><span data-stu-id="2c03a-192">It can take 15-20 minutes for the deployment to complete.</span></span>

## <a name="pricing"></a><span data-ttu-id="2c03a-193">価格</span><span class="sxs-lookup"><span data-stu-id="2c03a-193">Pricing</span></span>

<span data-ttu-id="2c03a-194">このシナリオの実行コストを調べることができるように、すべてのサービスがコスト計算ツールで事前構成されています。</span><span class="sxs-lookup"><span data-stu-id="2c03a-194">To explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span>  <span data-ttu-id="2c03a-195">特定のユース ケースについて価格の変化を確認するには、予想されるトラフィックに合わせて該当する変数を変更します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-195">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="2c03a-196">ご自身のアプリケーションを実行するスケール セット VM インスタンスの数に基づいて、3 つのサンプル コスト プロファイルが用意されています。</span><span class="sxs-lookup"><span data-stu-id="2c03a-196">We have provided three sample cost profiles based on the number of scale set VM instances that run your applications.</span></span>

* <span data-ttu-id="2c03a-197">[Small][small-pricing]: 2 つのフロントエンドおよび 2 つのバックエンド VM インスタンスに対応します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-197">[Small][small-pricing]: this correlates to two frontend and two backend VM instances.</span></span>
* <span data-ttu-id="2c03a-198">[Medium][medium-pricing]: 20 のフロントエンドおよび 5 つのバックエンド VM インスタンスに対応します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-198">[Medium][medium-pricing]: this correlates to 20 frontend and 5 backend VM instances.</span></span>
* <span data-ttu-id="2c03a-199">[Large][large-pricing]: 100 のフロントエンドおよび 10 つのバックエンド VM インスタンスに対応します。</span><span class="sxs-lookup"><span data-stu-id="2c03a-199">[Large][large-pricing]: this correlates to 100 frontend and 10 backend VM instances.</span></span>

## <a name="related-resources"></a><span data-ttu-id="2c03a-200">関連リソース</span><span class="sxs-lookup"><span data-stu-id="2c03a-200">Related Resources</span></span>

<span data-ttu-id="2c03a-201">このシナリオでは、Microsoft SQL Server クラスターを実行するバックエンド仮想マシン スケール セットを使用しました。</span><span class="sxs-lookup"><span data-stu-id="2c03a-201">This scenario used a backend virtual machine scale set that runs a Microsoft SQL Server cluster.</span></span> <span data-ttu-id="2c03a-202">アプリケーション データ用に、安全でスケーラブルなデータベース層として Azure Cosmos DB を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-202">Azure Cosmos DB could also be used as a scalable and secure database tier for the application data.</span></span> <span data-ttu-id="2c03a-203">[Azure 仮想ネットワーク サービス エンドポイント][vnetendpoint-docs]を使用すると、重要な Azure サービス リソースへのアクセスを仮想ネットワークのみに限定することができます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-203">An [Azure virtual network service endpoint][vnetendpoint-docs] allow you to secure your critical Azure service resources to only your virtual networks.</span></span> <span data-ttu-id="2c03a-204">このシナリオでは、VNet エンドポイントを使用することで、フロントエンド アプリケーション層と Cosmos DB の間のトラフィックをセキュリティで保護できます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-204">In this scenario, VNet endpoints allow you to secure traffic between the frontend application tier and Cosmos DB.</span></span> <span data-ttu-id="2c03a-205">Cosmos DB の詳細については、[Azure Cosmos DB の概要][azurecosmosdb-docs]に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2c03a-205">For more information on Cosmos DB, see [Azure Cosmos DB overview][azurecosmosdb-docs].</span></span>

<span data-ttu-id="2c03a-206">[SQL Server を使用汎用 n 層アプリケーションの詳しいリファレンス アーキテクチャ][ntiersql-ra]を確認することもできます。</span><span class="sxs-lookup"><span data-stu-id="2c03a-206">You also view a thorough [reference architecture for a generic N-tier application with SQL Server][ntiersql-ra].</span></span>

<!-- links -->
[appgateway-docs]: /azure/application-gateway/overview
[architecture]: ./media/regulated-multitier-app/architecture-regulated-multitier-app.png
[autoscaling]: /azure/architecture/best-practices/auto-scaling
[availability]: /architecture/checklist/availability
[azureaz-docs]: /azure/availability-zones/az-overview
[azurecosmosdb-docs]: /azure/cosmos-db/introduction
[cloudwitness-docs]: /windows-server/failover-clustering/deploy-cloud-witness
[loadbalancer-docs]: /azure/load-balancer/load-balancer-overview
[nsg-docs]: /azure/virtual-network/security-overview
[ntiersql-ra]: /azure/architecture/reference-architectures/n-tier/n-tier-sql-server
[resiliency]: /azure/architecture/resiliency/ 
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability 
[scaleset-docs]: /azure/virtual-machine-scale-sets/overview
[sqlalwayson-docs]: /sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server
[vmssautoscale-docs]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview
[vnet-docs]: /azure/virtual-network/virtual-networks-overview
[vnetendpoint-docs]: /azure/virtual-network/virtual-network-service-endpoints-overview
[pci-dss]: /azure/security/blueprints/pcidss-iaaswa-overview
[dmz]: /azure/virtual-network/virtual-networks-dmz-nsg
[cosmos]: /azure/cosmos-db/
[sql-linux]: /sql/linux/sql-server-linux-overview?view=sql-server-linux-2017

[small-pricing]: https://azure.com/e/711bbfcbbc884ef8aa91cdf0f2caff72
[medium-pricing]: https://azure.com/e/b622d82d79b34b8398c4bce35477856f
[large-pricing]: https://azure.com/e/1d99d8b92f90496787abecffa1473a93