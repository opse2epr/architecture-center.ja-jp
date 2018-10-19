---
title: Azure でのコンピューター支援エンジニアリング サービス
description: Azure で、コンピューター支援エンジニアリング (CAE) に、サービスとしてのソフトウェア (SaaS) プラットフォームを提供します。
author: alexbuckgit
ms.date: 08/22/2018
ms.openlocfilehash: d17ac218052c5b98e8790f1386be035618a2d957
ms.sourcegitcommit: b2a4eb132857afa70201e28d662f18458865a48e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48818685"
---
# <a name="a-computer-aided-engineering-service-on-azure"></a><span data-ttu-id="21729-103">Azure でのコンピューター支援エンジニアリング サービス</span><span class="sxs-lookup"><span data-stu-id="21729-103">A computer-aided engineering service on Azure</span></span>

<span data-ttu-id="21729-104">このシナリオ例では、Azure のハイ パフォーマンス コンピューティング (HPC) 機能を基に構築されたサービスとしてのソフトウェア (SaaS) プラットフォームの提供について示します。</span><span class="sxs-lookup"><span data-stu-id="21729-104">This example scenario demonstrates delivery of a software-as-a-service (SaaS) platform built on the high-performance computing (HPC) capabilities of Azure.</span></span> <span data-ttu-id="21729-105">このシナリオは、エンジニアリング ソフトウェア ソリューションが基になっています。</span><span class="sxs-lookup"><span data-stu-id="21729-105">This scenario is based on an engineering software solution.</span></span> <span data-ttu-id="21729-106">ただし、アーキテクチャは、画像レンダリング、複雑なモデリング、財務リスク計算など、HPC リソースを必要とする他の業界にも関連します。</span><span class="sxs-lookup"><span data-stu-id="21729-106">However, the architecture is relevant to other industries requiring HPC resources such as image rendering, complex modeling, and financial risk calculation.</span></span>

<span data-ttu-id="21729-107">この例で示すのは、コンピューター支援エンジニアリング (CAE) アプリケーションをエンジニアリング会社や製造企業に提供するエンジニアリング ソフトウェア プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="21729-107">This example demonstrates an engineering software provider that delivers computer-aided engineering (CAE) applications to engineering firms and manufacturing enterprises.</span></span> <span data-ttu-id="21729-108">CAE ソリューションにより、革新が可能になり、開発時間が短縮され、製品設計の全期間を通してコストが削減されます。</span><span class="sxs-lookup"><span data-stu-id="21729-108">CAE solutions enable innovation, reduce development times, and lower costs throughout the lifetime of a product's design.</span></span> <span data-ttu-id="21729-109">これらのソリューションでは、膨大な計算リソースが必要になり、多くの場合、大量のデータが処理されます。</span><span class="sxs-lookup"><span data-stu-id="21729-109">These solutions require substantial compute resources and often process high data volumes.</span></span> <span data-ttu-id="21729-110">高コストのオンプレミス HPC アプライアンスまたはハイエンド ワークステーションのため、小さいエンジニアリング会社や起業家や学生では、これらのテクノロジを利用できないことがよくあります。</span><span class="sxs-lookup"><span data-stu-id="21729-110">The high costs of an on-premises HPC appliance or high-end workstations often put these technologies out of reach for small engineering firms, entrepreneurs, and students.</span></span>

<span data-ttu-id="21729-111">このシナリオの会社は、クラウド ベースの HPC テクノロジを利用する SaaS プラットフォームを構築することにより、アプリケーションの市場を広げようと考えています。</span><span class="sxs-lookup"><span data-stu-id="21729-111">The company wants to expand the market for its applications by building a SaaS platform backed by cloud-based HPC technologies.</span></span> <span data-ttu-id="21729-112">顧客は、必要な計算リソースに対してだけ料金を支払い、他の手段では負担しきれない膨大なコンピューティング能力にアクセスできる必要があります。</span><span class="sxs-lookup"><span data-stu-id="21729-112">Their customers should be able to pay for compute resources as needed and access massive computing power that would be unaffordable otherwise.</span></span>

<span data-ttu-id="21729-113">会社の目標は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="21729-113">The company's goals include:</span></span>

* <span data-ttu-id="21729-114">Azure の HPC 機能を利用して、製品設計とテストのプロセスを高速化する。</span><span class="sxs-lookup"><span data-stu-id="21729-114">Taking advantage of HPC capabilities in Azure to accelerate the product design and testing process.</span></span>
* <span data-ttu-id="21729-115">ハードウェアの最新の革新を使用して複雑なシミュレーションを実行する一方で、単純なシミュレーションのコストは最小限に抑える。</span><span class="sxs-lookup"><span data-stu-id="21729-115">Using the latest hardware innovations to run complex simulations, while minimizing the costs for simpler simulations.</span></span>
* <span data-ttu-id="21729-116">ハイエンドのエンジニアリング ワークステーションを必要とせずに、Web ブラウザーで真に迫った視覚化とレンダリングを可能にする。</span><span class="sxs-lookup"><span data-stu-id="21729-116">Enabling true-to-life visualization and rendering in a web browser, without requiring a high-end engineering workstation.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="21729-117">関連するユース ケース</span><span class="sxs-lookup"><span data-stu-id="21729-117">Relevant use cases</span></span>

<span data-ttu-id="21729-118">このアーキテクチャを使用するシナリオとしては他に次のものがあります。</span><span class="sxs-lookup"><span data-stu-id="21729-118">Other scenarios using this architecture might include:</span></span>

* <span data-ttu-id="21729-119">ゲノミクス研究</span><span class="sxs-lookup"><span data-stu-id="21729-119">Genomics research</span></span>
* <span data-ttu-id="21729-120">天気シミュレーション</span><span class="sxs-lookup"><span data-stu-id="21729-120">Weather simulation</span></span>
* <span data-ttu-id="21729-121">計算化学アプリケーション</span><span class="sxs-lookup"><span data-stu-id="21729-121">Computational chemistry applications</span></span>

## <a name="architecture"></a><span data-ttu-id="21729-122">アーキテクチャ</span><span class="sxs-lookup"><span data-stu-id="21729-122">Architecture</span></span>

![HPC 機能を可能にする SaaS ソリューションのアーキテクチャ][architecture]

* <span data-ttu-id="21729-124">ユーザーは、[Apache Guacamole サービス](https://guacamole.apache.org/)を使用する HTML5 ベースの RDP 接続を備えたブラウザーを使用して、NV シリーズの仮想マシン (VM) にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="21729-124">Users can access NV-series virtual machines (VMs) via a browser with an HTML5-based RDP connection using the [Apache Guacamole service](https://guacamole.apache.org/).</span></span> <span data-ttu-id="21729-125">これらの VM インスタンスでは、レンダリングとコラボレーション タスクのための強力な GPU が提供されます。</span><span class="sxs-lookup"><span data-stu-id="21729-125">These VM instances provide powerful GPUs for rendering and collaborative tasks.</span></span> <span data-ttu-id="21729-126">ユーザーは、ハイエンドのモバイル コンピューティング デバイスまたはラップトップにアクセスしなくても、設計を編集し、その結果を表示できます。</span><span class="sxs-lookup"><span data-stu-id="21729-126">Users can edit their designs and view their results without needing access to high-end mobile computing devices or laptops.</span></span> <span data-ttu-id="21729-127">スケジューラにより、ユーザー定義のヒューリスティックに基づいて追加の VM が起動されます。</span><span class="sxs-lookup"><span data-stu-id="21729-127">The scheduler spins up additional VMs based on user-defined heuristics.</span></span>
* <span data-ttu-id="21729-128">デスクトップの CAD セッションから、ユーザーはワークロードを送信して使用可能な HPC クラスター ノードで実行できます。</span><span class="sxs-lookup"><span data-stu-id="21729-128">From a desktop CAD session, users can submit workloads for execution on available HPC cluster nodes.</span></span> <span data-ttu-id="21729-129">これらのワークロードで応力解析や計算流体力学の計算などのタスクが実行されるので、オンプレミスに専用の計算クラスターを用意する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="21729-129">These workloads perform tasks such as stress analysis or computational fluid dynamics calculations, eliminating the need for dedicated  on-premises compute clusters.</span></span> <span data-ttu-id="21729-130">これらのクラスター ノードは、計算リソースに対するアクティブなユーザーの要求に基づく負荷やキューの深さに応じて自動スケールするように構成できます。</span><span class="sxs-lookup"><span data-stu-id="21729-130">These cluster nodes can be configured to auto-scale based on load or queue depth based on active user demand for compute resources.</span></span>
* <span data-ttu-id="21729-131">エンド ユーザーが使用できる Web リソースをホストするには、Azure Kubernetes Service (AKS) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="21729-131">Azure Kubernetes Service (AKS) is used to host the web resources available to end users.</span></span>

### <a name="components"></a><span data-ttu-id="21729-132">コンポーネント</span><span class="sxs-lookup"><span data-stu-id="21729-132">Components</span></span>

* <span data-ttu-id="21729-133">[H シリーズ仮想マシン](/azure/virtual-machines/linux/sizes-hpc)は、分子モデリングや計算流体力学など、計算の多いシミュレーションの実行に使用されます。</span><span class="sxs-lookup"><span data-stu-id="21729-133">[H-series virtual machines](/azure/virtual-machines/linux/sizes-hpc) are used to run compute-intensive simulations such as molecular modeling and computational fluid dynamics.</span></span> <span data-ttu-id="21729-134">また、ソリューションでは、リモート ダイレクト メモリ アクセス (RDMA) 接続や InfiniBand ネットワークなどのテクノロジも利用されます。</span><span class="sxs-lookup"><span data-stu-id="21729-134">The solution also takes advantage of technologies like remote direct memory access (RDMA) connectivity and InfiniBand networking.</span></span>
* <span data-ttu-id="21729-135">[NV シリーズ仮想マシン](/azure/virtual-machines/windows/sizes-gpu)では、標準的な Web ブラウザーからエンジニアに対してハイエンド ワークステーションの機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="21729-135">[NV-series virtual machines](/azure/virtual-machines/windows/sizes-gpu) give engineers high-end workstation functionality from a standard web browser.</span></span> <span data-ttu-id="21729-136">これらの仮想マシンには、高度なレンダリングをサポートし、単精度のワークロードを実行できる、NVIDIA Tesla M60 GPU が装備されています。</span><span class="sxs-lookup"><span data-stu-id="21729-136">These virtual machines have NVIDIA Tesla M60 GPUs that support advanced rendering and can run single precision workloads.</span></span>
* <span data-ttu-id="21729-137">CentOS を実行する[汎用仮想マシン](/azure/virtual-machines/linux/sizes-general)では、Web アプリケーションなどの従来型のワークロードが処理されます。</span><span class="sxs-lookup"><span data-stu-id="21729-137">[General purpose virtual machines](/azure/virtual-machines/linux/sizes-general) running CentOS handle more traditional workloads such as web applications.</span></span>
* <span data-ttu-id="21729-138">[Application Gateway](/azure/application-gateway/overview) では、Web サーバーに届いた要求が負荷分散されます。</span><span class="sxs-lookup"><span data-stu-id="21729-138">[Application Gateway](/azure/application-gateway/overview) load balances the requests coming into the web servers.</span></span>
* <span data-ttu-id="21729-139">[Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) は、HPC または GPU 仮想マシンのハイエンド機能を必要としないシミュレーションのためのスケーラブルなワークロードを低コストで実行するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="21729-139">[Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) is used to run scalable workloads at a lower cost for simulations that don't require the high end capabilities of HPC or GPU virtual machines.</span></span>
* <span data-ttu-id="21729-140">[Altair PBS Works Suite](https://www.pbsworks.com/PBSProduct.aspx?n=PBS-Works-Suite&c=Overview-and-Capabilities) では HPC のワークフローが調整されて、現在の負荷を処理するのに十分な仮想マシン インスタンスを使用できることが保証されます。</span><span class="sxs-lookup"><span data-stu-id="21729-140">[Altair PBS Works Suite](https://www.pbsworks.com/PBSProduct.aspx?n=PBS-Works-Suite&c=Overview-and-Capabilities) orchestrates the HPC workflow, ensuring that enough virtual machine instances are available to handle the current load.</span></span> <span data-ttu-id="21729-141">また、需要が低下したときはコストを削減するために仮想マシンの割り当てが解除されます。</span><span class="sxs-lookup"><span data-stu-id="21729-141">It also deallocates virtual machines when demand is lower to reduce costs.</span></span>
* <span data-ttu-id="21729-142">[BLOB ストレージ](/azure/storage/blobs/storage-blobs-introduction)には、スケジュールされたジョブをサポートするファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="21729-142">[Blob storage](/azure/storage/blobs/storage-blobs-introduction) stores files that support the scheduled jobs.</span></span> 

### <a name="alternatives"></a><span data-ttu-id="21729-143">代替手段</span><span class="sxs-lookup"><span data-stu-id="21729-143">Alternatives</span></span>

* <span data-ttu-id="21729-144">[Azure CycleCloud](/azure/cyclecloud/overview) を使用すると、HPC クラスターの作成、管理、運用、最適化が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="21729-144">[Azure CycleCloud](/azure/cyclecloud/overview) simplifies creating, managing, operating, and optimizing HPC clusters.</span></span> <span data-ttu-id="21729-145">高度なポリシーとガバナンスの機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="21729-145">It offers advanced policy and governance features.</span></span> <span data-ttu-id="21729-146">CycleCloud では、任意のジョブ スケジューラまたはソフトウェア スタックがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="21729-146">CycleCloud supports any job scheduler or software stack.</span></span>
* <span data-ttu-id="21729-147">[HPC Pack](/azure/virtual-machines/windows/hpcpack-cluster-options) では、Windows Server ベースのワークロード用の Azure HPC クラスターを作成して管理できます。</span><span class="sxs-lookup"><span data-stu-id="21729-147">[HPC Pack](/azure/virtual-machines/windows/hpcpack-cluster-options) can create and manage an Azure HPC cluster for Windows Server-based workloads.</span></span> <span data-ttu-id="21729-148">Linux ベースのワークロードには HPC Pack を使用できません。</span><span class="sxs-lookup"><span data-stu-id="21729-148">HPC Pack isn't an option for Linux-based workloads.</span></span>
* <span data-ttu-id="21729-149">[Azure Automation State Configuration](/azure/automation/automation-dsc-overview) では、展開する仮想マシンとソフトウェアの定義に対して、コードとしてのインフラストラクチャのアプローチが提供されます。</span><span class="sxs-lookup"><span data-stu-id="21729-149">[Azure Automation State Configuration](/azure/automation/automation-dsc-overview) provides an infrastructure-as-code approach to defining the virtual machines and software to be deployed.</span></span> <span data-ttu-id="21729-150">ジョブ キューに送信されたジョブの数に基づく計算ノードの自動スケール ルールを使用し、仮想マシン スケール セットの一部として仮想マシンを展開できます。</span><span class="sxs-lookup"><span data-stu-id="21729-150">Virtual machines can be deployed as part of a virtual machine scale set, with auto-scaling rules for compute nodes based on the number of jobs submitted to the job queue.</span></span> <span data-ttu-id="21729-151">新しい仮想マシンが必要なときは、Azure イメージ ギャラリーから最新のパッチが適用されたイメージを使用して仮想マシンがプロビジョニングされた後、必要なソフトウェアがインストールされ、PowerShell DSC 構成スクリプトを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="21729-151">When a new virtual machine is needed, it is provisioned using the latest patched image from the Azure image gallery, and then the required software is installed and configured via a PowerShell DSC configuration script.</span></span>
* [<span data-ttu-id="21729-152">Azure Functions</span><span class="sxs-lookup"><span data-stu-id="21729-152">Azure Functions</span></span>](/azure/azure-functions/functions-overview)

## <a name="considerations"></a><span data-ttu-id="21729-153">考慮事項</span><span class="sxs-lookup"><span data-stu-id="21729-153">Considerations</span></span>

* <span data-ttu-id="21729-154">コードとしてのインフラストラクチャのアプローチは仮想マシンのビルド定義を管理する優れた方法ですが、スクリプトを使用して新しい仮想マシンをプロビジョニングするには長い時間がかかることがあります。</span><span class="sxs-lookup"><span data-stu-id="21729-154">While using an infrastructure-as-code approach is a great way to manage virtual machine build definitions, it can take a long time to provision a new virtual machine using a script.</span></span> <span data-ttu-id="21729-155">このソリューションでは、DSC スクリプトを使用してゴールデン イメージを定期的に作成することで、適当な妥協点が見つかりました。このイメージを使用すると、DSC を使用し、需要に応じて仮想マシンを完全に構築より速く、新しい仮想マシンをプロビジョニングできます。</span><span class="sxs-lookup"><span data-stu-id="21729-155">This solution found a good middle ground by using the DSC script to periodically create a golden image, which can then be used to provision a new virtual machine faster than completely building a VM on demand using DSC.</span></span> <span data-ttu-id="21729-156">Azure DevOps Services または他の CI/CD ツールを使用すると、DSC スクリプトを使用してゴールデン イメージを定期的に更新できます。</span><span class="sxs-lookup"><span data-stu-id="21729-156">Azure DevOps Services or other CI/CD tooling can periodically refresh golden images using DSC scripts.</span></span>
* <span data-ttu-id="21729-157">ソリューションの全体的なコストと、計算リソースの高速の可用性のバランスを取ることは、重要な考慮事項です。</span><span class="sxs-lookup"><span data-stu-id="21729-157">Balancing overall solution costs with fast availability of compute resources is a key consideration.</span></span> <span data-ttu-id="21729-158">N シリーズ仮想マシン インスタンスのプールをプロビジョニングし、それを割り当て解除された状態にすると、運用コストが減ります。</span><span class="sxs-lookup"><span data-stu-id="21729-158">Provisioning a pool of N-series virtual machine instances and putting them in a deallocated state lowers the operating costs.</span></span> <span data-ttu-id="21729-159">仮想マシンを追加する必要があるときの、既存インスタンスの再割り当てには、別のホストでの仮想マシンの起動が伴いますが、仮想マシンをプロビジョニング解除してから再プロビジョニングすると再起動時に GPU に対して同じ PCI バスが保持されるので、OS が GPU 用のドライバーを識別してインストールするために必要な PCI バス検出時間が除去されます。</span><span class="sxs-lookup"><span data-stu-id="21729-159">When an additional virtual machine is needed, reallocating an existing instance will involve powering up the virtual machine on a different host, but the PCI bus detection time required by the OS to identify and install drivers for the GPU is eliminated because a virtual machine that is deprovisioned and then reprovisioned will retain the same PCI bus for the GPU upon restart.</span></span>
* <span data-ttu-id="21729-160">元のアーキテクチャでは、シミュレーションの実行を Azure 仮想マシンに完全に依存していました。</span><span class="sxs-lookup"><span data-stu-id="21729-160">The original architecture relied entirely on Azure virtual machines for running simulations.</span></span> <span data-ttu-id="21729-161">仮想マシンのすべての機能を必要としないワークロードのコストを削減するため、これらのワークロードはコンテナー化されて、Azure Kubernetes Service (AKS) に展開されました。</span><span class="sxs-lookup"><span data-stu-id="21729-161">In order to reduce costs for workloads that didn't require all the capabilities of a virtual machine, these workloads were containerized and deployed to Azure Kubernetes Service (AKS).</span></span>
* <span data-ttu-id="21729-162">会社の従業員は、オープン ソース テクノロジについてのスキルを既に備えていました。</span><span class="sxs-lookup"><span data-stu-id="21729-162">The company's workforce had existing skills in open-source technologies.</span></span> <span data-ttu-id="21729-163">Linux や Kubernetes などのテクノロジを基にすることで、これらのスキルを活用できます。</span><span class="sxs-lookup"><span data-stu-id="21729-163">They can take advantage of these skills by building on technologies like Linux and Kubernetes.</span></span> 

## <a name="pricing"></a><span data-ttu-id="21729-164">価格</span><span class="sxs-lookup"><span data-stu-id="21729-164">Pricing</span></span>

<span data-ttu-id="21729-165">このシナリオの実行コストを調べることができるよう、必要なサービスの多くは、[料金計算ツール][calculator]で事前構成されています。</span><span class="sxs-lookup"><span data-stu-id="21729-165">To help you explore the cost of running this scenario, many of the required services are pre-configured in a [cost calculator example][calculator].</span></span> <span data-ttu-id="21729-166">ソリューションのコストは、要件を満たすために必要なサービスの数とスケールに依存します。</span><span class="sxs-lookup"><span data-stu-id="21729-166">The costs of your solution are dependent on the number and scale of services needed to meet your requirements.</span></span>

<span data-ttu-id="21729-167">以下の考慮事項によって、このソリューションのコストのかなりの部分が決まります。</span><span class="sxs-lookup"><span data-stu-id="21729-167">The following considerations will drive a substantial portion of the costs for this solution:</span></span>
* <span data-ttu-id="21729-168">Azure の仮想マシンのコストは、インスタンスが追加プロビジョニングされのに比例して増加します。</span><span class="sxs-lookup"><span data-stu-id="21729-168">Azure virtual machine costs increase linearly as additional instances are provisioned.</span></span> <span data-ttu-id="21729-169">割り当てを解除された仮想マシンについては、ストレージ コストのみが発生し、コンピューティング コストはかかりません。</span><span class="sxs-lookup"><span data-stu-id="21729-169">Virtual machines that are deallocated will only incur storage costs, and not compute costs.</span></span> <span data-ttu-id="21729-170">割り当てを解除されたマシンは、需要が増えたら再割り当てできます。</span><span class="sxs-lookup"><span data-stu-id="21729-170">These deallocated machines can then be reallocated when demand is high.</span></span>
* <span data-ttu-id="21729-171">Azure Kubernetes Service のコストは、ワークロードをサポートするために選択された VM の種類に基づきます。</span><span class="sxs-lookup"><span data-stu-id="21729-171">Azure Kubernetes Services costs are based on the VM type chosen to support the workload.</span></span> <span data-ttu-id="21729-172">コストは、クラスター内の VM の数に比例して増加します。</span><span class="sxs-lookup"><span data-stu-id="21729-172">The costs will increase linearly based on the number of VMs in the cluster.</span></span>

## <a name="next-steps"></a><span data-ttu-id="21729-173">次の手順</span><span class="sxs-lookup"><span data-stu-id="21729-173">Next Steps</span></span>

* <span data-ttu-id="21729-174">[Altair 社の導入事例][source-document]を読んでください。</span><span class="sxs-lookup"><span data-stu-id="21729-174">Read the [Altair customer story][source-document].</span></span> <span data-ttu-id="21729-175">このシナリオ例は、そのアーキテクチャのバージョンが基になっています。</span><span class="sxs-lookup"><span data-stu-id="21729-175">This example scenario is based on a version of their architecture.</span></span>
* <span data-ttu-id="21729-176">Azure で利用できる他の[ビッグ コンピューティング ソリューション](https://azure.microsoft.com/solutions/big-compute)を確認してください。</span><span class="sxs-lookup"><span data-stu-id="21729-176">Review other [Big Compute solutions](https://azure.microsoft.com/solutions/big-compute) available in Azure.</span></span>

<!-- links -->
[architecture]: ./media/architecture-hpc-saas.png
[source-document]: https://customers.microsoft.com/story/altair-manufacturing-azure
[calculator]: https://azure.com/e/3cb9ccdc893f41ffbcdb00c328178ccf