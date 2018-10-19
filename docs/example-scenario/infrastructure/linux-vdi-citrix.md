---
title: Citrix を使用した Linux 仮想デスクトップ
description: Azure で Citrix を使用して Linux デスクトップ向けの VDI 環境を構築します。
author: miguelangelopereira
ms.date: 09/12/2018
ms.openlocfilehash: 374d59f7a528bd89870baa601a49a30ea00a08f1
ms.sourcegitcommit: b2a4eb132857afa70201e28d662f18458865a48e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48819144"
---
# <a name="linux-virtual-desktops-with-citrix"></a><span data-ttu-id="91a1c-103">Citrix を使用した Linux 仮想デスクトップ</span><span class="sxs-lookup"><span data-stu-id="91a1c-103">Linux Virtual Desktops with Citrix</span></span>

<span data-ttu-id="91a1c-104">このシナリオの例は、Linux デスクトップ用の仮想デスクトップインフラストラクチャ (VDI) が必要なあらゆる業界に当てはまります。</span><span class="sxs-lookup"><span data-stu-id="91a1c-104">This example scenario is applicable to any industry that needs a Virtual Desktop Infrastructure (VDI) for Linux Desktops.</span></span> <span data-ttu-id="91a1c-105">VDI とは、データセンター内のサーバー上で稼働する仮想マシン内でユーザーのデスクトップを実行するプロセスを指します。</span><span class="sxs-lookup"><span data-stu-id="91a1c-105">VDI refers to the process of running a user desktop inside a virtual machine that lives on a server in the datacenter.</span></span> <span data-ttu-id="91a1c-106">このシナリオで、顧客は VDI のニーズを満たすために Citrix ベースのソリューションを使用することを選んでいます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-106">The customer in this scenario chose to use a Citrix-based solution for their VDI needs.</span></span>

<span data-ttu-id="91a1c-107">従業員が複数のデバイスやオペレーティング システムを使用する異機種混合環境の組織は少なくありません。</span><span class="sxs-lookup"><span data-stu-id="91a1c-107">Organizations often have heterogeneous environments with multiple devices and operating systems being used by employees.</span></span> <span data-ttu-id="91a1c-108">セキュリティで保護された環境を維持しながら、アプリケーションへの一貫したアクセスを提供することは容易ではありません。</span><span class="sxs-lookup"><span data-stu-id="91a1c-108">It can be challenging to provide consistent access to applications while maintaining a secure environment.</span></span> <span data-ttu-id="91a1c-109">Linux デスクトップ用の VDI ソリューションを利用するなら、組織はエンド ユーザーが使用するデバイスや OS に関係なく、アクセスを提供することができます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-109">A VDI solution for Linux desktops will allow your organization to provide access irrespective of the device or OS used by the end user.</span></span>

<span data-ttu-id="91a1c-110">このサンプル ソリューションの利点のいくつかを以下に示します。</span><span class="sxs-lookup"><span data-stu-id="91a1c-110">Some benefits for this sample solution include the following:</span></span>
* <span data-ttu-id="91a1c-111">共有 Linux 仮想デスクトップを利用してより多くのユーザーが同じインフラストラクチャにアクセスできるようにすることで、投資収益率が高くなります。</span><span class="sxs-lookup"><span data-stu-id="91a1c-111">Return on investment will be higher with shared Linux virtual desktops by giving more users access to the same infrastructure.</span></span> <span data-ttu-id="91a1c-112">一元化された VDI 環境にリソースを統合することで、求められるエンド ユーザーのデバイスのスペックが低くなります。</span><span class="sxs-lookup"><span data-stu-id="91a1c-112">By consolidating resources on a centralized VDI environment, the end user devices don't need to be as powerful.</span></span>
* <span data-ttu-id="91a1c-113">エンド ユーザーのデバイスに関係なく一貫したパフォーマンスが得られます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-113">Performance will be consistent regardless of the end user device.</span></span>
* <span data-ttu-id="91a1c-114">ユーザーは、Linux アプリケーションに (Linux 以外のデバイスを含む) あらゆるデバイスからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-114">Users can access Linux applications from any device (including non-Linux devices).</span></span>
* <span data-ttu-id="91a1c-115">各地に分散して配置されている全従業員の機密データを Azure データ センターで保護できます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-115">Sensitive data can be secured in the Azure data center for all distributed employees.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="91a1c-116">関連するユース ケース</span><span class="sxs-lookup"><span data-stu-id="91a1c-116">Relevant use cases</span></span>

<span data-ttu-id="91a1c-117">次のユース ケースでこのシナリオをご検討ください。</span><span class="sxs-lookup"><span data-stu-id="91a1c-117">Consider this scenario for the following use case:</span></span>

* <span data-ttu-id="91a1c-118">特化されたミッション クリティカルな Linux VDI デスクトップに Linux または Linux 以外のデバイスから安全にアクセスできるようにする</span><span class="sxs-lookup"><span data-stu-id="91a1c-118">Providing secure access to mission-critical, specialized Linux VDI desktops from Linux or non-Linux devices</span></span>

## <a name="architecture"></a><span data-ttu-id="91a1c-119">アーキテクチャ</span><span class="sxs-lookup"><span data-stu-id="91a1c-119">Architecture</span></span>

<span data-ttu-id="91a1c-120">[![](./media/azure-citrix-sample-diagram.png "アーキテクチャ ダイアグラム")](./media/azure-citrix-sample-diagram.png#lightbox)</span><span class="sxs-lookup"><span data-stu-id="91a1c-120">[![](./media/azure-citrix-sample-diagram.png "Architecture Diagram")](./media/azure-citrix-sample-diagram.png#lightbox)</span></span>

<span data-ttu-id="91a1c-121">このシナリオの例では、企業ネットワークから Linux 仮想デスクトップにアクセスできるようにする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="91a1c-121">This example scenario demonstrates allowing the corporate network to access the Linux Virtual Desktops:</span></span>

* <span data-ttu-id="91a1c-122">オンプレミスの環境と Azure の間に ExpressRoute が確立されます。これにより、クラウドへの高速で信頼性の高い接続が可能になります。</span><span class="sxs-lookup"><span data-stu-id="91a1c-122">An ExpressRoute is established between the on-premises environment and Azure, for fast and reliable connectivity to the cloud.</span></span>
* <span data-ttu-id="91a1c-123">VDI 用に Citrix XenDeskop ソリューションがデプロイされています。</span><span class="sxs-lookup"><span data-stu-id="91a1c-123">Citrix XenDeskop solution deployed for VDI.</span></span>
* <span data-ttu-id="91a1c-124">Ubuntu (またはサポートされている別のディストリビューション) で CitrixVDA が実行されます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-124">The CitrixVDA run on Ubuntu (or another supported distro).</span></span>
* <span data-ttu-id="91a1c-125">Azure ネットワーク セキュリティ グループによって正しいネットワーク ACL が適用されます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-125">Azure Network Security Groups will apply the correct network ACLs.</span></span>
* <span data-ttu-id="91a1c-126">Citrix ADC (NetScaler) がCitrix のすべてのサービスを発行し、負荷分散します。</span><span class="sxs-lookup"><span data-stu-id="91a1c-126">Citrix ADC (NetScaler) will publish and load balance all the Citrix services.</span></span>
* <span data-ttu-id="91a1c-127">Citrix サーバーにドメイン参加するために Active Directory Domain Services が使用されます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-127">Active Directory Domain Services will be used to domain join the Citrix servers.</span></span> <span data-ttu-id="91a1c-128">VDA サーバーでは、ドメイン参加は行われません。</span><span class="sxs-lookup"><span data-stu-id="91a1c-128">VDA servers will not be domain joined.</span></span>
* <span data-ttu-id="91a1c-129">Azure Hybrid File Sync により、ソリューション全体で記憶域を共有できます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-129">Azure Hybrid File Sync will enable shared storage across the solution.</span></span> <span data-ttu-id="91a1c-130">たとえば、リモート ソリューションでもホーム ソリューションでも使用できます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-130">For example, it can be used in remote/home solutions.</span></span>

<span data-ttu-id="91a1c-131">このシナリオでは、次の SKU が使用されます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-131">For this scenario, the following SKUs are used:</span></span>

- <span data-ttu-id="91a1c-132">Citrix ADC (NetScaler): 2 x D4s v3 ([NetScaler 12.0 VPX Standard Edition 200 MBPS PAYG イメージ](https://azuremarketplace.microsoft.com/pt-br/marketplace/apps/citrix.netscalervpx-120?tab=PlansAndPrice)を使用)</span><span class="sxs-lookup"><span data-stu-id="91a1c-132">Citrix ADC (NetScaler): 2 x D4sv3 with [NetScaler 12.0 VPX Standard Edition 200 MBPS PAYG image](https://azuremarketplace.microsoft.com/pt-br/marketplace/apps/citrix.netscalervpx-120?tab=PlansAndPrice)</span></span>
- <span data-ttu-id="91a1c-133">Citrix License Server: 1 x D2s v3</span><span class="sxs-lookup"><span data-stu-id="91a1c-133">Citrix License Server: 1 x D2s v3</span></span>
- <span data-ttu-id="91a1c-134">Citrix VDA: 4 x D8s v3</span><span class="sxs-lookup"><span data-stu-id="91a1c-134">Citrix VDA: 4 x D8s v3</span></span>
- <span data-ttu-id="91a1c-135">Citrix Storefront: 2 x D2s v3</span><span class="sxs-lookup"><span data-stu-id="91a1c-135">Citrix Storefront: 2 x D2s v3</span></span>
- <span data-ttu-id="91a1c-136">Citrix Delivery Controller: 2 x D2s v3</span><span class="sxs-lookup"><span data-stu-id="91a1c-136">Citrix Delivery Controller: 2 x D2s v3</span></span>
- <span data-ttu-id="91a1c-137">ドメイン コントローラー: 2 x D2s v3</span><span class="sxs-lookup"><span data-stu-id="91a1c-137">Domain Controllers: 2 x D2sv3</span></span>
- <span data-ttu-id="91a1c-138">Azure ファイル サーバー: 2 x D2s v3</span><span class="sxs-lookup"><span data-stu-id="91a1c-138">Azure File Servers: 2 x D2sv3</span></span>

> [!NOTE]
> <span data-ttu-id="91a1c-139">すべてのライセンス (NetScaler を除く) は、ライセンス持ち込み (BYOL) です</span><span class="sxs-lookup"><span data-stu-id="91a1c-139">All the licenses (other than NetScaler) are bring-your-own-license (BYOL)</span></span>

### <a name="components"></a><span data-ttu-id="91a1c-140">コンポーネント</span><span class="sxs-lookup"><span data-stu-id="91a1c-140">Components</span></span>

- <span data-ttu-id="91a1c-141">[Azure Virtual Network](/azure/virtual-network/virtual-networks-overview) を使用すると、VM などのリソースが、他のリソース、インターネット、オンプレミスのネットワークと安全に通信することができます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-141">[Azure Virtual Network](/azure/virtual-network/virtual-networks-overview) allows resources such as VMs to securely communicate with each other, the internet, and on-premises networks.</span></span> <span data-ttu-id="91a1c-142">仮想ネットワークにより、分離性、セグメント化、トラフィックのフィルター処理とルーティングが提供され、場所間の接続が可能になります。</span><span class="sxs-lookup"><span data-stu-id="91a1c-142">Virtual networks provide isolation and segmentation, filter and route traffic, and allow connection between locations.</span></span> <span data-ttu-id="91a1c-143">このシナリオでは、すべてのリソースに対して 1 つの仮想ネットワークが使用されます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-143">One virtual network will be used for all resources in this scenario.</span></span>
- <span data-ttu-id="91a1c-144">[Azure ネットワーク セキュリティ グループ](/azure/virtual-network/security-overview)には、ソースまたはターゲット IP アドレス、ポート、プロトコルを基に、受信/送信ネットワーク トラフィックを許可または拒否するセキュリティ規則の一覧が含まれています。</span><span class="sxs-lookup"><span data-stu-id="91a1c-144">[Azure network security groups](/azure/virtual-network/security-overview) contain a list of security rules that allow or deny inbound or outbound network traffic based on source or destination IP address, port, and protocol.</span></span> <span data-ttu-id="91a1c-145">このシナリオの仮想ネットワークは、アプリケーション コンポーネント間のトラフィック フローを制限するネットワーク セキュリティ グループ規則によって保護されています。</span><span class="sxs-lookup"><span data-stu-id="91a1c-145">The virtual networks in this scenario are secured with network security group rules that restrict the flow of traffic between the application components.</span></span>
- <span data-ttu-id="91a1c-146">[Azure Load Balancer](/azure/application-gateway/overview) は、規則と正常性プローブに従って受信トラフィックを分散します。</span><span class="sxs-lookup"><span data-stu-id="91a1c-146">[Azure load balancer](/azure/application-gateway/overview) distributes inbound traffic according to rules and health probes.</span></span> <span data-ttu-id="91a1c-147">ロード バランサーは、低遅延と高スループットを実現できるだけでなく、あらゆる TCP アプリケーションと UDP アプリケーションの数百万ものフローにスケールアップできます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-147">A load balancer provides low latency and high throughput, and scales up to millions of flows for all TCP and UDP applications.</span></span> <span data-ttu-id="91a1c-148">このシナリオでは、Citrix NetScaler のトラフィックの分散に内部ロード バランサーを使用します。</span><span class="sxs-lookup"><span data-stu-id="91a1c-148">An internal load balancer is used in this scenario to distribute traffic on the Citrix NetScaler.</span></span>
- <span data-ttu-id="91a1c-149">すべての共有ストレージで [Azure Hybrid File Sync](https://github.com/MicrosoftDocs/azure-docs/edit/master/articles/storage/files/storage-sync-files-planning.md) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-149">[Azure Hybrid File Sync](https://github.com/MicrosoftDocs/azure-docs/edit/master/articles/storage/files/storage-sync-files-planning.md) will be used for all shared storage.</span></span> <span data-ttu-id="91a1c-150">ストレージは、Hybrid File Sync を使用して 2 つのファイル サーバーにレプリケートされます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-150">The storage will replicate to two file servers using Hybrid File Sync.</span></span>
- <span data-ttu-id="91a1c-151">[Azure SQL Database](/azure/sql-database/sql-database-technical-overview) は、Microsoft SQL Server データベース エンジンの安定した最新バージョンに基づく、サービスとしてのリレーショナル データベース (DBaaS) です。</span><span class="sxs-lookup"><span data-stu-id="91a1c-151">[Azure SQL Database](/azure/sql-database/sql-database-technical-overview) is a relational database-as-a-service (DBaaS) based on the latest stable version of Microsoft SQL Server Database Engine.</span></span> <span data-ttu-id="91a1c-152">これは、Citrix のデータベースをホストするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-152">It will be used for hosting Citrix databases.</span></span>
- <span data-ttu-id="91a1c-153">[ExpressRoute](/azure/expressroute/expressroute-introduction) を利用すると、接続プロバイダーが提供するプライベート接続を介して、オンプレミスのネットワークを Microsoft クラウドに拡張できます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-153">[ExpressRoute](/azure/expressroute/expressroute-introduction) lets you extend your on-premises networks into the Microsoft cloud over a private connection facilitated by a connectivity provider.</span></span> 
- <span data-ttu-id="91a1c-154">Active Directory Domain Services はディレクトリ サービスとユーザー認証に使用されます</span><span class="sxs-lookup"><span data-stu-id="91a1c-154">[Active Directory Domain Services is used for Directory Services and user authentication</span></span>
- <span data-ttu-id="91a1c-155">[Azure 可用性セット](/azure/virtual-machines/windows/tutorial-availability-sets)は、Azure にデプロイされる VM を、クラスター内の複数の分離されたハードウェア ノードに分散させます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-155">[Azure Availabilty Sets](/azure/virtual-machines/windows/tutorial-availability-sets) will ensure that the VMs you deploy on Azure are distributed across multiple isolated hardware nodes in a cluster.</span></span> <span data-ttu-id="91a1c-156">これにより、Azure 内でハードウェアまたはソフトウェアの障害が発生した場合に影響を受けるのは VM のサブセットに限定され、ソリューション全体は引き続き利用可能であり、運用可能であることが保証されます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-156">Doing this ensures that if a hardware or software failure within Azure happens, only a subset of your VMs are impacted and that your overall solution remains available and operational.</span></span> 
- <span data-ttu-id="91a1c-157">[Citrix ADC (NetScaler)](https://www.citrix.com/products/citrix-adc) は、アプリケーション固有のトラフィック分析を実行して、Web アプリケーション用のレイヤー 4 からレイヤー 7 (L4-L7) のネットワーク トラフィックをインテリジェントに配布、最適化、保護するアプリケーション配信コントローラーです。</span><span class="sxs-lookup"><span data-stu-id="91a1c-157">[Citrix ADC (NetScaler)](https://www.citrix.com/products/citrix-adc) is an application delivery controller that performs application-specific traffic analysis to intelligently distribute, optimize, and secure Layer 4-Layer 7 (L4–L7) network traffic for web applications.</span></span> 
- <span data-ttu-id="91a1c-158">[Citrix Storefront](https://www.citrix.com/products/citrix-virtual-apps-and-desktops/citrix-storefront.html) は、セキュリティを強化し、デプロイを簡素化するエンタープライズ アプリ ストアです。他に例を見ない、モダンでほぼネイティブのユーザー エクスペリエンスを、あらゆるプラットフォーム上の Citrix Receiver で提供します。</span><span class="sxs-lookup"><span data-stu-id="91a1c-158">[Citrix Storefront](https://www.citrix.com/products/citrix-virtual-apps-and-desktops/citrix-storefront.html) is an enterprise app store that improves security and simplifies deployments, delivering a modern, unmatched near-native user experience across Citrix Receiver on any platform.</span></span> <span data-ttu-id="91a1c-159">StoreFront を使用すると、マルチサイトおよびマルチ バージョンの Citrix Virtual Apps and Desktops 環境を簡単に管理できます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-159">StoreFront makes it easy to manage multi-site and multi-version Citrix Virtual Apps and Desktops environments.</span></span> 
- <span data-ttu-id="91a1c-160">[Citrix License Server](https://www.citrix.com/buy/licensing/overview.html) は、Citrix 製品のライセンスを管理します。</span><span class="sxs-lookup"><span data-stu-id="91a1c-160">[Citrix License Server](https://www.citrix.com/buy/licensing/overview.html) will manage the licenses for Citrix products.</span></span>
- <span data-ttu-id="91a1c-161">[Citrix XenDesktops VDA](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service) を使用すると、アプリケーションとデスクトップに接続できます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-161">[Citrix XenDesktops VDA](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service) enables connections to applications and desktops.</span></span> <span data-ttu-id="91a1c-162">VDA は、ユーザーのためにアプリケーションまたは仮想デスクトップを実行するマシンにインストールされます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-162">The VDA is installed on the machine that runs the applications or virtual desktops for the user.</span></span> <span data-ttu-id="91a1c-163">これを使用することで、マシンをデリバリー コントローラーに登録して、ユーザー デバイスへの High Definition eXperience (HDX) 接続を管理することができるようになります。</span><span class="sxs-lookup"><span data-stu-id="91a1c-163">It enables the machines to register with Delivery Controllers and manage the High Definition eXperience (HDX) connection to a user device.</span></span>
- <span data-ttu-id="91a1c-164">[Citrix Delivery Controllers](https://docs.citrix.com/en-us/xenapp-and-xendesktop/7-15-ltsr/manage-deployment/delivery-controllers) は、ユーザー アクセスの管理、および接続の仲介と最適化を担当するサーバー側のコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="91a1c-164">[Citrix Delivery Controller](https://docs.citrix.com/en-us/xenapp-and-xendesktop/7-15-ltsr/manage-deployment/delivery-controllers) is the server-side component responsible for managing user access, plus brokering and optimizing connections.</span></span> <span data-ttu-id="91a1c-165">コントローラーには、デスクトップとサーバーのイメージを作成する Machine Creation Services も備えられています。</span><span class="sxs-lookup"><span data-stu-id="91a1c-165">Controllers also provide the Machine Creation Services that create desktop and server images.</span></span>

### <a name="alternatives"></a><span data-ttu-id="91a1c-166">代替手段</span><span class="sxs-lookup"><span data-stu-id="91a1c-166">Alternatives</span></span>

- <span data-ttu-id="91a1c-167">複数のパートナーが、VMware や Workspot など、Azure でサポートされている VDI ソリューションを用意しています。</span><span class="sxs-lookup"><span data-stu-id="91a1c-167">There are multiple partners with VDI solutions that supported in Azure such as VMware, Workspot, and others.</span></span> <span data-ttu-id="91a1c-168">この特定のサンプル アーキテクチャは、Citrix を使用したデプロイ済みプロジェクトに基づいています。</span><span class="sxs-lookup"><span data-stu-id="91a1c-168">This specific sample architecture is based on a deployed project that used Citrix.</span></span>
- <span data-ttu-id="91a1c-169">Citrix は、このアーキテクチャの一部を抽象化するクラウド サービスを提供しています。</span><span class="sxs-lookup"><span data-stu-id="91a1c-169">Citrix provides a cloud service that abstracts part of this architecture.</span></span> <span data-ttu-id="91a1c-170">そのサービスは、このソリューションの代替手段となり得ます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-170">It could be an alternative for this solution.</span></span> <span data-ttu-id="91a1c-171">詳細については、[Citrix Cloud](https://www.citrix.com/products/citrix-cloud) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91a1c-171">For more information, see [Citrix Cloud](https://www.citrix.com/products/citrix-cloud).</span></span>

## <a name="considerations"></a><span data-ttu-id="91a1c-172">考慮事項</span><span class="sxs-lookup"><span data-stu-id="91a1c-172">Considerations</span></span>

- <span data-ttu-id="91a1c-173">[Citrix の Linux 要件](https://docs.citrix.com/en-us/linux-virtual-delivery-agent/current-release/system-requirements)をご確認ください。</span><span class="sxs-lookup"><span data-stu-id="91a1c-173">Check the [Citrix Linux Requirements](https://docs.citrix.com/en-us/linux-virtual-delivery-agent/current-release/system-requirements).</span></span>
- <span data-ttu-id="91a1c-174">待機時間は、ソリューション全体に影響を与えることがあります。</span><span class="sxs-lookup"><span data-stu-id="91a1c-174">Latency can have impact on the overall solution.</span></span> <span data-ttu-id="91a1c-175">運用環境では、必要に応じてテストを行ってください。</span><span class="sxs-lookup"><span data-stu-id="91a1c-175">For a production environment, test accordingly.</span></span>
- <span data-ttu-id="91a1c-176">シナリオによっては、VDA 用の GPU を備えた VM がソリューションに必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="91a1c-176">Depending on the scenario, the solution may need VMs with GPUs for VDA.</span></span> <span data-ttu-id="91a1c-177">このソリューションは、GPU が要件ではないことを前提としています。</span><span class="sxs-lookup"><span data-stu-id="91a1c-177">For this solution, it is assumed that GPU is not a requirement.</span></span>

### <a name="availability-scalability-and-security"></a><span data-ttu-id="91a1c-178">可用性、スケーラビリティ、セキュリティ</span><span class="sxs-lookup"><span data-stu-id="91a1c-178">Availability, Scalability, and Security</span></span>

- <span data-ttu-id="91a1c-179">このサンプル ソリューションは、ライセンス サーバーを除くすべての役割が高可用性を持つよう設計されています。</span><span class="sxs-lookup"><span data-stu-id="91a1c-179">This sample solution is designed for high availability for all roles other than the licensing server.</span></span> <span data-ttu-id="91a1c-180">ライセンス サーバーがオフラインになっても、30 日間の猶予期間にわたって環境は機能し続けるため、ライセンス サーバーに追加の冗長性は不要です。</span><span class="sxs-lookup"><span data-stu-id="91a1c-180">Because the environment continues to function during a 30-day grace period if the license server is offline, no additional redundancy is required on that server.</span></span>
- <span data-ttu-id="91a1c-181">同様の役割を提供するすべてのサーバーを[可用性セット](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)にデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="91a1c-181">All servers providing similar roles should be deployed in [Availability Sets](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).</span></span>
- <span data-ttu-id="91a1c-182">このサンプル ソリューションには、ディザスター リカバリー機能は含まれていません。</span><span class="sxs-lookup"><span data-stu-id="91a1c-182">This sample solution does not include Disaster Recovery capabilities.</span></span> <span data-ttu-id="91a1c-183">これを設計に組み込む場合は、[Azure Site Recovery](/azure/site-recovery/site-recovery-overview) をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="91a1c-183">[Azure Site Recovery](/azure/site-recovery/site-recovery-overview) could be a good add-on to this design.</span></span>
- <span data-ttu-id="91a1c-184">運用環境では、[バックアップ](/azure/backup/backup-introduction-to-azure-backup)、[監視](/azure/monitoring-and-diagnostics/monitoring-overview)、[更新管理](/azure/automation/automation-update-management)などのデプロイ管理ソリューションを実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="91a1c-184">For a production deployment management solution should be implemented such as [backup](/azure/backup/backup-introduction-to-azure-backup), [monitoring](/azure/monitoring-and-diagnostics/monitoring-overview) and [update management](/azure/automation/automation-update-management).</span></span>
- <span data-ttu-id="91a1c-185">このサンプル ソリューションは、約 250 人 (VDA サーバーあたり約 50 - 60 人) のユーザーがさまざまな用途で同時に使用できます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-185">This sample solution should work for about 250 concurrent (about 50-60 per VDA server) users with a mixed usage.</span></span> <span data-ttu-id="91a1c-186">しかし、これは使用するアプリケーションの種類によって大いに異なります。</span><span class="sxs-lookup"><span data-stu-id="91a1c-186">But that will greatly depended on the type of applications being used.</span></span> <span data-ttu-id="91a1c-187">運用環境で使用する場合は、厳しいロード テストを実施する必要があります。</span><span class="sxs-lookup"><span data-stu-id="91a1c-187">For production use, rigorous load testing should be performed.</span></span>

## <a name="deploy-this-scenario"></a><span data-ttu-id="91a1c-188">このシナリオのデプロイ</span><span class="sxs-lookup"><span data-stu-id="91a1c-188">Deploy this scenario</span></span>

<span data-ttu-id="91a1c-189">デプロイの詳細については、公式の [Citrix ドキュメント](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/install-configure.html)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91a1c-189">For deployment information see the official [Citrix documentation](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/install-configure.html).</span></span>

## <a name="pricing"></a><span data-ttu-id="91a1c-190">価格</span><span class="sxs-lookup"><span data-stu-id="91a1c-190">Pricing</span></span>

- <span data-ttu-id="91a1c-191">Citrix XenDestop ライセンスは、Azure サービスの料金には含まれません。</span><span class="sxs-lookup"><span data-stu-id="91a1c-191">The Citrix XenDestop licenses are not included in Azure service charges.</span></span>
- <span data-ttu-id="91a1c-192">Citrix NetScaler ライセンスは、従量課金制のモデルに含まれます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-192">The Citrix NetScaler license is included in a pay-as-you-go model.</span></span>
- <span data-ttu-id="91a1c-193">予約インスタンスを使用すると、ソリューションの計算コストが大幅に削減されます。</span><span class="sxs-lookup"><span data-stu-id="91a1c-193">Using reserved instances will greatly reduce the compute cost for the solution.</span></span>
- <span data-ttu-id="91a1c-194">ExpressRoute の料金は含まれません。</span><span class="sxs-lookup"><span data-stu-id="91a1c-194">The ExpressRoute cost is not included.</span></span>

## <a name="next-steps"></a><span data-ttu-id="91a1c-195">次の手順</span><span class="sxs-lookup"><span data-stu-id="91a1c-195">Next Steps</span></span>

- <span data-ttu-id="91a1c-196">計画とデプロイについては、[こちらから](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/install-configure) Citrix ドキュメントをご確認ください。</span><span class="sxs-lookup"><span data-stu-id="91a1c-196">Check Citrix documentation for planning and deployment [here](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/install-configure).</span></span>
- <span data-ttu-id="91a1c-197">Azure に Citrix ADC (NetScaler) をデプロイするには、Citrix によって提供されている Resource Manager テンプレートを[こちら](https://github.com/citrix/netscaler-azure-templates)でご確認ください。</span><span class="sxs-lookup"><span data-stu-id="91a1c-197">To deploy Citrix ADC (NetScaler) in Azure, review the Resource Manager templates provided by Citrix [here](https://github.com/citrix/netscaler-azure-templates).</span></span>