---
title: 'CAF: Azure のしくみ'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
description: Azure の内部機能の説明
author: petertaylor9999
ms.date: 02/11/2019
ms.openlocfilehash: 215e4e4954a2f3e722e3ac865fea19508f6edadd
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068822"
---
<!-- markdownlint-disable MD026 -->

# <a name="how-does-azure-work"></a><span data-ttu-id="bd135-103">Azure のしくみ</span><span class="sxs-lookup"><span data-stu-id="bd135-103">How does Azure work?</span></span>

<span data-ttu-id="bd135-104">Azure は、Microsoft のパブリック クラウド プラットフォームです。</span><span class="sxs-lookup"><span data-stu-id="bd135-104">Azure is Microsoft's public cloud platform.</span></span> <span data-ttu-id="bd135-105">Azure では、サービスとしてのプラットフォーム (PaaS)、サービスとしてのインフラストラクチャ (IaaS)、サービスとしてのデータベース (DBaaS) 機能をはじめ、他の多くのサービスを含む、大規模なサービス コレクションを提供しています。</span><span class="sxs-lookup"><span data-stu-id="bd135-105">Azure offers a large collection of services including platform as a service (PaaS), infrastructure as a service (IaaS), database as a service (DBaaS) capabilities.</span></span> <span data-ttu-id="bd135-106">しかし、そもそも Azure とはどんなもので、どのように動作するのでしょう。</span><span class="sxs-lookup"><span data-stu-id="bd135-106">But what exactly is Azure, and how does it work?</span></span>

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ixGo]

<!-- markdownlint-enable MD034 -->

<span data-ttu-id="bd135-107">他のクラウド プラットフォームと同様に、Azure は**仮想化**と呼ばれる技術に依存しています。</span><span class="sxs-lookup"><span data-stu-id="bd135-107">Azure, like other cloud platforms, relies on a technology known as **virtualization**.</span></span> <span data-ttu-id="bd135-108">ほとんどのコンピューター ハードウェアは単純に、シリコン単位で永続的または半永続的にエンコードされた命令のセットであるため、ソフトウェアでエミュレートできます。</span><span class="sxs-lookup"><span data-stu-id="bd135-108">Most computer hardware can be emulated in software, because most computer hardware is simply a set of instructions permanently or semi-permanently encoded in silicon.</span></span> <span data-ttu-id="bd135-109">ソフトウェアの命令をハードウェアの命令にマップするエミュレーション レイヤーを使用して、仮想化されたハードウェアが実際のハードウェア自体であるかのように、ソフトウェアで実行できます。</span><span class="sxs-lookup"><span data-stu-id="bd135-109">Using an emulation layer that maps software instructions to hardware instructions, virtualized hardware can execute in software as if it were the actual hardware itself.</span></span>

<span data-ttu-id="bd135-110">基本的に、クラウドとは、顧客に代わって仮想化されたハードウェアを実行する、1 つまたは複数のデータセンターにある物理サーバーのセットです。</span><span class="sxs-lookup"><span data-stu-id="bd135-110">Essentially, the cloud is a set of physical servers in one or more datacenters that execute virtualized hardware on behalf of customers.</span></span> <span data-ttu-id="bd135-111">では、クラウドはどのようにして、膨大な数の顧客に対する、仮想化されたハードウェアの大量のインスタンスを作成、開始、停止、および削除しているのでしょうか。</span><span class="sxs-lookup"><span data-stu-id="bd135-111">So how does the cloud create, start, stop, and delete millions of instances of virtualized hardware for millions of customers simultaneously?</span></span>

<span data-ttu-id="bd135-112">これを理解するために、データセンターのハードウェアのアーキテクチャを見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="bd135-112">To understand this, let's look at the architecture of the hardware in the datacenter.</span></span>  <span data-ttu-id="bd135-113">各データセンターの内部は、サーバー ラックに置かれているサーバーのコレクションです。</span><span class="sxs-lookup"><span data-stu-id="bd135-113">Inside each datacenter is a collection of servers sitting in server racks.</span></span> <span data-ttu-id="bd135-114">各サーバー ラックには、ネットワーク接続と電力配分装置 (PDU) を提供するネットワーク スイッチと共に、多数のサーバー **ブレード**が格納されています。</span><span class="sxs-lookup"><span data-stu-id="bd135-114">Each server rack contains many server **blades** as well as a network switch providing network connectivity and a power distribution unit (PDU) providing power.</span></span> <span data-ttu-id="bd135-115">場合によっては、ラックは、**クラスター**と呼ばれるより大規模な単位にグループ化されています。</span><span class="sxs-lookup"><span data-stu-id="bd135-115">Racks are sometimes grouped together in larger units known as **clusters**.</span></span>

<span data-ttu-id="bd135-116">各ラックまたはクラスター内では、ほとんどのサーバーが、ユーザーに代わってこれらの仮想化されたハードウェア インスタンスを実行するように指示されています。</span><span class="sxs-lookup"><span data-stu-id="bd135-116">Within each rack or cluster, most of the servers are designated to run these virtualized hardware instances on behalf of the user.</span></span> <span data-ttu-id="bd135-117">しかし、一部のサーバーでは、ファブリック コントローラーと呼ばれるクラウド管理ソフトウェアが実行されます。</span><span class="sxs-lookup"><span data-stu-id="bd135-117">However, some of the servers run cloud management software known as a fabric controller.</span></span> <span data-ttu-id="bd135-118">**ファブリック コントローラー**は、さまざまな役割を担う分散アプリケーションです。</span><span class="sxs-lookup"><span data-stu-id="bd135-118">The **fabric controller** is a distributed application with many responsibilities.</span></span> <span data-ttu-id="bd135-119">ファブリック コントローラーはサービスを割り当て、実行しているサーバーおよびサービスの正常性を監視し、エラーが発生した場合はサーバーを回復させます。</span><span class="sxs-lookup"><span data-stu-id="bd135-119">It allocates services, monitors the health of the server and the services running on it, and heals servers when they fail.</span></span>

<span data-ttu-id="bd135-120">ファブリック コントローラーの各インスタンスは、通常は**フロント エンド**と呼ばれるクラウド オーケストレーション ソフトウェアを実行している別のサーバー セットに接続されます。</span><span class="sxs-lookup"><span data-stu-id="bd135-120">Each instance of the fabric controller is connected to another set of servers running cloud orchestration software, typically known as a **front end**.</span></span> <span data-ttu-id="bd135-121">フロント エンドでは、Web サービス、RESTful API、およびクラウドが実行するすべての関数で使用される内部 Azure データベースをホストしています。</span><span class="sxs-lookup"><span data-stu-id="bd135-121">The front end hosts the web services, RESTful APIs, and internal Azure databases used for all functions the cloud performs.</span></span>

<span data-ttu-id="bd135-122">たとえば、フロントエンドは、顧客の要求を処理するサービスをホストして、Azure リソース (たとえば、[仮想ネットワーク](/azure/virtual-network/virtual-networks-overview)、[仮想マシン](/azure/virtual-machines)、[Cosmos DB](/azure/cosmos-db/introduction) などのサービス) を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="bd135-122">For example, the front end hosts the services that handle customer requests to allocate Azure resources such as [virtual networks](/azure/virtual-network/virtual-networks-overview), [virtual machines](/azure/virtual-machines), and services like [Cosmos DB](/azure/cosmos-db/introduction).</span></span> <span data-ttu-id="bd135-123">最初に、フロント エンドはユーザーを検証して、要求されたリソースを割り当てる権限がそのユーザーにあることを確認します。</span><span class="sxs-lookup"><span data-stu-id="bd135-123">First, the front end validates the user and verifies the user is authorized to allocate the requested resources.</span></span> <span data-ttu-id="bd135-124">権限がある場合、フロント エンドはデータベースを確認して十分な容量のあるサーバー ラックを検出し、そのラックのファブリック コントローラーにリソースを割り当てるよう命令します。</span><span class="sxs-lookup"><span data-stu-id="bd135-124">If so, the front end checks a database to locate a server rack with sufficient capacity and then instructs the fabric controller on that rack to allocate the resource.</span></span>

<span data-ttu-id="bd135-125">そのため基本的に、Azure は膨大なサーバーおよびネットワーク ハードウェアのコレクションであり、これは、これらのサーバー上にある仮想化されたハードウェアおよびソフトウェアの構成および操作をうまく調整する分散アプリケーションの複雑なセットを実行します。</span><span class="sxs-lookup"><span data-stu-id="bd135-125">So fundamentally, Azure is a huge collection of servers and networking hardware running a complex set of distributed applications to orchestrate the configuration and operation of the virtualized hardware and software on those servers.</span></span> <span data-ttu-id="bd135-126">Azure を強化しているのは、この調整機能です。Azure がバックグラウンドでハードウェアの保守やアップグレードをすべて行うので、ユーザーは、これらを担う必要がなくなりました。</span><span class="sxs-lookup"><span data-stu-id="bd135-126">It is this orchestration that makes Azure so powerful &mdash; users are no longer responsible for maintaining and upgrading hardware because Azure does all this behind the scenes.</span></span>

## <a name="next-steps"></a><span data-ttu-id="bd135-127">次の手順</span><span class="sxs-lookup"><span data-stu-id="bd135-127">Next steps</span></span>

<span data-ttu-id="bd135-128">Azure 内部について理解できました。次に、クラウド リソース ガバナンスについて確認します。</span><span class="sxs-lookup"><span data-stu-id="bd135-128">Now that you understand Azure internals, learn about cloud resource governance.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="bd135-129">リソース ガバナンスについて確認する</span><span class="sxs-lookup"><span data-stu-id="bd135-129">Learn about resource governance</span></span>](what-is-governance.md)

<!-- Links -->

[docs-add-users-to-aad]: /azure/active-directory/add-users-azure-active-directory?toc=/azure/architecture/cloud-adoption-guide/toc.json
