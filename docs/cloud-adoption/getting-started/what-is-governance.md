---
title: 'CAF: クラウド リソース ガバナンスとは'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.date: 02/11/2019
description: Azure でのクラウド リソース ガバナンスの説明
author: petertaylor9999
ms.openlocfilehash: 0989a5aad8a6290cce07fd71771c690bbd615e0d
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068856"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-cloud-resource-governance"></a><span data-ttu-id="6f7e3-103">クラウド リソース ガバナンスとは</span><span class="sxs-lookup"><span data-stu-id="6f7e3-103">What is cloud resource governance?</span></span>

<span data-ttu-id="6f7e3-104">[Azure のしくみ](what-is-azure.md)に関するページでは、Azure は、仮想化されたハードウェアとソフトウェアをユーザーの代わりに実行する、サーバーとネットワーク ハードウェアのコレクションであることを説明しました。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-104">In [How does Azure work?](what-is-azure.md), you learned that Azure is a collection of servers and networking hardware running virtualized hardware and software on behalf of users.</span></span> <span data-ttu-id="6f7e3-105">Azure を使用すると、必要に応じてリソースを容易に作成、読み取り、更新、および削除できるようにすることで、組織のアプリケーション開発および IT 部門の機敏性を高めることが可能です。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-105">Azure enables your organization's application development and IT departments to be agile by making it easy to create, read, update, and delete resources as needed.</span></span>

<span data-ttu-id="6f7e3-106">ただし、リソースに無制限にアクセスできるため、開発者は非常に機敏になり、予想外のコストが発生する可能性もあります。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-106">However, while unrestricted access to resources can make developers very agile, it can also lead to unexpected costs.</span></span> <span data-ttu-id="6f7e3-107">たとえば、テスト用に一連のリソースのデプロイを承認された開発チームが、テスト完了後に、そのリソースを削除し忘れることがあります。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-107">For example, a development team might be approved to deploy a set of resources for testing but forget to delete them when testing is complete.</span></span> <span data-ttu-id="6f7e3-108">これらのリソースのコストは、承認が無効になったり不要になったりしても発生し続けます。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-108">These resources will continue to accrue costs even though they are no longer approved or necessary.</span></span>

<span data-ttu-id="6f7e3-109">これを解決するのが、リソース アクセス ガバナンスです。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-109">The solution is resource access governance.</span></span> <span data-ttu-id="6f7e3-110">**ガバナンス**とは、組織の要件を満たすために、Azure リソースの使用を継続的に管理、監視、および監査するプロセスを意味します。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-110">**Governance** is the ongoing process of managing, monitoring, and auditing the use of Azure resources to meet the requirements of your organization.</span></span>

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ii94]

<!-- markdownlint-enable MD034 -->

<span data-ttu-id="6f7e3-111">これらの要件は組織によって異なるため、ガバナンスへの 1 つのアプローチでの対応は役立ちません。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-111">These requirements are unique to each organization, so a one-size-fits-all approach to governance isn't helpful.</span></span> <span data-ttu-id="6f7e3-112">代わりに、Azure の 2 つの主要ガバナンス ツール (**ロール ベースのアクセス制御 (RBAC)** と**リソース ポリシー**) を使用してガバナンス モデルを設計することは、それぞれの組織の責任です。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-112">Instead, it's up to each organization to design their governance model using Azure's two primary governance tools: **role-based access control (RBAC)** and **resource policy**.</span></span>

<span data-ttu-id="6f7e3-113">RBAC ではロールが定義され、そのロールによって、ロールに割り当てられている各ユーザーの機能が定義されます。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-113">RBAC defines roles, and roles define the capabilities of each user assigned that role.</span></span> <span data-ttu-id="6f7e3-114">たとえば、**所有者**ロールでは、リソースのすべての機能 (作成、読み取り、更新、および削除) が許可されますが、**閲覧者**ロールでは、読み取り機能しか許可されません。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-114">For example, the **owner** role allows all capabilites (create, read, update, and delete) for a resource, while the  **reader** role allows only the read capability.</span></span> <span data-ttu-id="6f7e3-115">広範なスコープでロールを定義して、多くの種類のリソースに適用することも、スコープを狭くして適用対象を少なくすることもできます。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-115">Roles can be defined with a broad scope that applies to many resource types, or a narrow scope that applies to a few.</span></span>

<span data-ttu-id="6f7e3-116">リソース ポリシーでは、リソース作成のルールが定義されます。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-116">Resource policies define rules for resource creation.</span></span> <span data-ttu-id="6f7e3-117">たとえば、リソース ポリシーによって、仮想マシンの SKU を、事前に承認された特定のサイズに制限できます。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-117">For example, a resource policy can limit the SKU of a virtual machine to a particular pre-approved size.</span></span> <span data-ttu-id="6f7e3-118">リソースの作成が要求されたときに、別のリソース ポリシーによって、割り当てられたコスト センターに対するタグの適用を強制できます。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-118">Another resource policy could enforce the application of a tag for an assigned cost center when the request is made to create the resource.</span></span>

<span data-ttu-id="6f7e3-119">これらのツールを構成するときは、ガバナンスと組織の機敏性のバランスを保つことが重要です。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-119">When configuring these tools, it is important to balance governance and organizational agility.</span></span> <span data-ttu-id="6f7e3-120">ガバナンス ポリシーの制限が厳しいほど、開発者や IT 作業者の機敏性が低下します。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-120">The more restrictive your governance policy, the less agile your developers and IT workers will be.</span></span> <span data-ttu-id="6f7e3-121">ガバナンス ポリシーの制限が厳しいと、開発者がフォームを入力するか、ガバナンス チームのメンバーにメールして、リソースを手動で作成しなければならない、といった手動による操作が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-121">A restrictive governance policy may require more manual steps like requiring a developer to fill out a form or send an email to a member of the governance team to manually create a resource.</span></span> <span data-ttu-id="6f7e3-122">ガバナンス チームの能力には限りがあり、ボトルネックが発生する可能性があります。これにより、リソースが作成されるのを待っている開発チームの生産性が低下したり、リソースが削除される前に不要なリソースによりコストが発生したりします。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-122">The governance team has finite capacity and may become a bottleneck, resulting in development teams waiting unproductively for their resources to be created or unneeded resources accruing costs before they are deleted.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6f7e3-123">次の手順</span><span class="sxs-lookup"><span data-stu-id="6f7e3-123">Next steps</span></span>

<span data-ttu-id="6f7e3-124">クラウド リソース ガバナンスの概念を理解したので、Azure でリソース アクセスを管理する方法について確認します。</span><span class="sxs-lookup"><span data-stu-id="6f7e3-124">Now that you understand the concept of cloud resource governance, learn more about how resource access is managed in Azure.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="6f7e3-125">Azure でのリソース アクセスについて確認する</span><span class="sxs-lookup"><span data-stu-id="6f7e3-125">Learn about resource access in Azure</span></span>](azure-resource-access.md)
