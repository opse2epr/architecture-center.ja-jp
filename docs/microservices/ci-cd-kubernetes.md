---
title: Kubernetes 上でのマイクロサービス用の CI/CD パイプラインの構築
description: Azure Kubernetes Service (AKS) にマイクロサービスをデプロイするための CI/CD パイプラインの例を説明します。
author: MikeWasson
ms.date: 04/11/2019
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: microservices
ms.openlocfilehash: e7da8a1b4111fb7856b919b40d033833a4e475e0
ms.sourcegitcommit: d58e6b2b891c9c99e951c59f15fce71addcb96b1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59533143"
---
<!-- markdownlint-disable MD040 -->

# <a name="building-a-cicd-pipeline-for-microservices-on-kubernetes"></a><span data-ttu-id="85afe-103">Kubernetes 上でのマイクロサービス用の CI/CD パイプラインの構築</span><span class="sxs-lookup"><span data-stu-id="85afe-103">Building a CI/CD pipeline for microservices on Kubernetes</span></span>

<span data-ttu-id="85afe-104">マイクロサービス アーキテクチャ用に信頼性の高い CI/CD プロセスを作成するのは、困難な場合があります。</span><span class="sxs-lookup"><span data-stu-id="85afe-104">It can be challenging to create a reliable CI/CD process for a microservices architecture.</span></span> <span data-ttu-id="85afe-105">個々のチームは、他のチームを妨害したり、アプリケーション全体を不安定にしたりすることなく、サービスを迅速かつ確実にリリースできる必要があります。</span><span class="sxs-lookup"><span data-stu-id="85afe-105">Individual teams must be able to release services quickly and reliably, without disrupting other teams or destabilizing the application as a whole.</span></span>

<span data-ttu-id="85afe-106">この記事では、Azure Kubernetes Service (AKS) にマイクロサービスをデプロイするための CI/CD パイプラインの例を説明します。</span><span class="sxs-lookup"><span data-stu-id="85afe-106">This article describes an example CI/CD pipeline for deploying microservices to Azure Kubernetes Service (AKS).</span></span> <span data-ttu-id="85afe-107">すべてのチームやプロジェクトはそれぞれ異なるので、この記事を一連の厳格な規則としては考えないでください。</span><span class="sxs-lookup"><span data-stu-id="85afe-107">Every team and project is different, so don't take this article as a set of hard-and-fast rules.</span></span> <span data-ttu-id="85afe-108">その代わり、独自の CI/CD プロセスを設計するための出発点になります。</span><span class="sxs-lookup"><span data-stu-id="85afe-108">Instead, it's meant to be a starting point for designing your own CI/CD process.</span></span>

<span data-ttu-id="85afe-109">ここで説明するパイプラインの例は、[GitHub][ri] 上で確認できるドローン配送アプリケーションと呼ばれるマイクロサービス参照実装のために作成されたものです。</span><span class="sxs-lookup"><span data-stu-id="85afe-109">The example pipeline described here was created for a microservices reference implementation called the Drone Delivery application, which you can find on [GitHub][ri].</span></span> <span data-ttu-id="85afe-110">アプリケーションのシナリオについては、[こちら](./design/index.md#reference-implementation)に記載されています。</span><span class="sxs-lookup"><span data-stu-id="85afe-110">The application scenario is described [here](./design/index.md#reference-implementation).</span></span>

<span data-ttu-id="85afe-111">パイプラインの目標は、次のようにまとめることができます。</span><span class="sxs-lookup"><span data-stu-id="85afe-111">The goals of the pipeline can be summarized as follows:</span></span>

- <span data-ttu-id="85afe-112">チームは、サービスを個別に構築およびデプロイすることができます。</span><span class="sxs-lookup"><span data-stu-id="85afe-112">Teams can build and deploy their services independently.</span></span>
- <span data-ttu-id="85afe-113">CI プロセスに合格したコード変更は、運用環境に似た環境に自動的にデプロイされます。</span><span class="sxs-lookup"><span data-stu-id="85afe-113">Code changes that pass the CI process are automatically deployed to a production-like environment.</span></span>
- <span data-ttu-id="85afe-114">品質ゲートは、パイプラインの各段階で適用されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-114">Quality gates are enforced at each stage of the pipeline.</span></span>
- <span data-ttu-id="85afe-115">サービスの新しいバージョンは、以前のバージョンと並行してデプロイできます。</span><span class="sxs-lookup"><span data-stu-id="85afe-115">A new version of a service can be deployed side by side with the previous version.</span></span>

<span data-ttu-id="85afe-116">背景の詳細については、[マイクロサービス アーキテクチャの CI/CD](./ci-cd.md) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="85afe-116">For more background, see [CI/CD for microservices architectures](./ci-cd.md).</span></span>

## <a name="assumptions"></a><span data-ttu-id="85afe-117">前提条件</span><span class="sxs-lookup"><span data-stu-id="85afe-117">Assumptions</span></span>

<span data-ttu-id="85afe-118">この例の目的として、開発チームとコード ベースに関するいくつかの想定を次に示します。</span><span class="sxs-lookup"><span data-stu-id="85afe-118">For purposes of this example, here are some assumptions about the development team and the code base:</span></span>

- <span data-ttu-id="85afe-119">コード リポジトリは単一リポジトリであり、フォルダーがマイクロサービス別に整理されています。</span><span class="sxs-lookup"><span data-stu-id="85afe-119">The code repository is a monorepo, with folders organized by microservice.</span></span>
- <span data-ttu-id="85afe-120">チームのブランチ戦略は、[トランクベース開発](https://trunkbaseddevelopment.com/)に基づいています。</span><span class="sxs-lookup"><span data-stu-id="85afe-120">The team's branching strategy is based on [trunk-based development](https://trunkbaseddevelopment.com/).</span></span>
- <span data-ttu-id="85afe-121">チームは、[リリース ブランチ](/azure/devops/repos/git/git-branching-guidance?view=azure-devops#manage-releases)を使用してリリースを管理します。</span><span class="sxs-lookup"><span data-stu-id="85afe-121">The team uses [release branches](/azure/devops/repos/git/git-branching-guidance?view=azure-devops#manage-releases) to manage releases.</span></span> <span data-ttu-id="85afe-122">マイクロサービスごとに個別のリリースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-122">Separate releases are created for each microservice.</span></span>
- <span data-ttu-id="85afe-123">CI/CD プロセスでは、[Azure Pipelines](/azure/devops/pipelines/?view=azure-devops) を使用してマイクロサービスの AKS への構築、テスト、デプロイが行われます。</span><span class="sxs-lookup"><span data-stu-id="85afe-123">The CI/CD process uses [Azure Pipelines](/azure/devops/pipelines/?view=azure-devops) to build, test, and deploy the microservices to AKS.</span></span>
- <span data-ttu-id="85afe-124">各マイクロサービスのコンテナー イメージは、[Azure Container Registry](/azure/container-registry/)に格納されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-124">The container images for each microservice are stored in [Azure Container Registry](/azure/container-registry/).</span></span>
- <span data-ttu-id="85afe-125">チームは、Helm chart を使用して各マイクロサービスをパッケージ化します。</span><span class="sxs-lookup"><span data-stu-id="85afe-125">The team uses Helm charts to package each microservice.</span></span>

<span data-ttu-id="85afe-126">これらの想定によって、CI/CD パイプラインの特定の詳細の多くが設定されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-126">These assumptions drive many of the specific details of the CI/CD pipeline.</span></span> <span data-ttu-id="85afe-127">ただし、ここで説明する基本的な手法は、Jenkins や Docker Hub など、他のプロセス、ツール、サービスにも適用されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-127">However, the basic approach described here be adapted for other processes, tools, and services, such as Jenkins or Docker Hub.</span></span>

## <a name="validation-builds"></a><span data-ttu-id="85afe-128">検証ビルド</span><span class="sxs-lookup"><span data-stu-id="85afe-128">Validation builds</span></span>

<span data-ttu-id="85afe-129">開発者は Delivery Service と呼ばれるマイクロサービスに取り組んでいるとします。</span><span class="sxs-lookup"><span data-stu-id="85afe-129">Suppose that a developer is working on a microservice called the Delivery Service.</span></span> <span data-ttu-id="85afe-130">新機能の開発中に、開発者は、機能ブランチにコードをチェックインします。</span><span class="sxs-lookup"><span data-stu-id="85afe-130">While developing a new feature, the developer checks code into a feature branch.</span></span> <span data-ttu-id="85afe-131">慣例により、機能ブランチは `feature/*` と名付けられます。</span><span class="sxs-lookup"><span data-stu-id="85afe-131">By convention, feature branches are named `feature/*`.</span></span>

![CI/CD ワークフロー](./images/aks-cicd-1.png)

<span data-ttu-id="85afe-133">ビルド定義ファイルには、次のように、ブランチ名とソース パスでフィルター処理するトリガーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="85afe-133">The build definition file includes a trigger that filters by the branch name and the source path:</span></span>

```yaml
trigger:
  batch: true
  branches:
    include:
    - master
    - feature/*
    - topic/*

    exclude:
    - feature/experimental/*
    - topic/experimental/*

  paths:
     include:
     - /src/shipping/delivery/
```

<span data-ttu-id="85afe-134">&#11162; [ソース ファイル](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-validation.yml)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="85afe-134">&#11162; See the [source file](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-validation.yml).</span></span>

<span data-ttu-id="85afe-135">このアプローチを使用して、各チームは、専用のビルド パイプラインを持つことができます。</span><span class="sxs-lookup"><span data-stu-id="85afe-135">Using this approach, each team can have its own build pipeline.</span></span> <span data-ttu-id="85afe-136">`/src/shipping/delivery` フォルダーにチェックインされているコードのみによって、Delivery Service のビルドがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="85afe-136">Only code that is checked into the `/src/shipping/delivery` folder triggers a build of the Delivery Service.</span></span> <span data-ttu-id="85afe-137">フィルターに一致するブランチにコミットをプッシュすると、CI のビルドがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="85afe-137">Pushing commits to a branch that matches the filter triggers a CI build.</span></span> <span data-ttu-id="85afe-138">ワークフローのこの時点で、CI ビルドでは、いくつかの最小限のコードの検証が実行されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-138">At this point in the workflow, the CI build runs some minimal code verification:</span></span>

1. <span data-ttu-id="85afe-139">コードをビルドする。</span><span class="sxs-lookup"><span data-stu-id="85afe-139">Build the code.</span></span>
1. <span data-ttu-id="85afe-140">単体テストを実行する。</span><span class="sxs-lookup"><span data-stu-id="85afe-140">Run unit tests.</span></span>

<span data-ttu-id="85afe-141">ビルド時間を短くして、開発者がすばやくフィードバックを取得できるようにすることが目標です。</span><span class="sxs-lookup"><span data-stu-id="85afe-141">The goal is to keep build times short, so the developer can get quick feedback.</span></span> <span data-ttu-id="85afe-142">開発者は、機能をマスターにマージする準備ができたら、PR を開きます。</span><span class="sxs-lookup"><span data-stu-id="85afe-142">Once the feature is ready to merge into master, the developer opens a PR.</span></span> <span data-ttu-id="85afe-143">これにより、いくつかの追加のチェックを実行する別の CI ビルドがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="85afe-143">This triggers another CI build that performs some additional checks:</span></span>

1. <span data-ttu-id="85afe-144">コードをビルドする。</span><span class="sxs-lookup"><span data-stu-id="85afe-144">Build the code.</span></span>
1. <span data-ttu-id="85afe-145">単体テストを実行する。</span><span class="sxs-lookup"><span data-stu-id="85afe-145">Run unit tests.</span></span>
1. <span data-ttu-id="85afe-146">ランタイム コンテナー イメージをビルドする。</span><span class="sxs-lookup"><span data-stu-id="85afe-146">Build the runtime container image.</span></span>
1. <span data-ttu-id="85afe-147">イメージに対して脆弱性スキャンを実行する。</span><span class="sxs-lookup"><span data-stu-id="85afe-147">Run vulnerability scans on the image.</span></span>

![CI/CD ワークフロー](./images/aks-cicd-2.png)

> [!NOTE]
> <span data-ttu-id="85afe-149">Azure DevOps Repos では、ブランチを保護するための[ポリシー](/azure/devops/repos/git/branch-policies)を定義できます。</span><span class="sxs-lookup"><span data-stu-id="85afe-149">In Azure DevOps Repos, you can define [policies](/azure/devops/repos/git/branch-policies) to protect branches.</span></span> <span data-ttu-id="85afe-150">たとえば、マスターにマージするには、CI ビルドの成功に加え、承認者のサインオフが必要であることをポリシーで要求できます。</span><span class="sxs-lookup"><span data-stu-id="85afe-150">For example, the policy could require a successful CI build plus a sign-off from an approver in order to merge into master.</span></span>

## <a name="full-cicd-build"></a><span data-ttu-id="85afe-151">完全な CI/CD ビルド</span><span class="sxs-lookup"><span data-stu-id="85afe-151">Full CI/CD build</span></span>

<span data-ttu-id="85afe-152">ある時点で、チームは、この Delivery サービスの新しいバージョンをデプロイする準備が整います。</span><span class="sxs-lookup"><span data-stu-id="85afe-152">At some point, the team is ready to deploy a new version of the Delivery service.</span></span> <span data-ttu-id="85afe-153">リリース マネージャーで、`release/<microservice name>/<semver>` という名前付けパターンを使用して、マスターからブランチを作成します。</span><span class="sxs-lookup"><span data-stu-id="85afe-153">The release manager creates a branch from master with this naming pattern: `release/<microservice name>/<semver>`.</span></span> <span data-ttu-id="85afe-154">たとえば、「 `release/delivery/v1.0.2` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="85afe-154">For example, `release/delivery/v1.0.2`.</span></span>

![CI/CD ワークフロー](./images/aks-cicd-3.png)

<span data-ttu-id="85afe-156">このブランチを作成すると、これまでのすべての手順に加え、以下を実行する完全な CI ビルドがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="85afe-156">Creation of this branch triggers a full CI build that runs all of the previous steps plus:</span></span>

1. <span data-ttu-id="85afe-157">Azure Container Registry にコンテナー イメージをプッシュする。</span><span class="sxs-lookup"><span data-stu-id="85afe-157">Push the container image to Azure Container Registry.</span></span> <span data-ttu-id="85afe-158">イメージには、ブランチ名から取得されたバージョン番号がタグ付けされます。</span><span class="sxs-lookup"><span data-stu-id="85afe-158">The image is tagged with the version number taken from the branch name.</span></span>
2. <span data-ttu-id="85afe-159">`helm package` を実行して、サービス用の Helm chart をパッケージ化する。</span><span class="sxs-lookup"><span data-stu-id="85afe-159">Run `helm package` to package the Helm chart for the service.</span></span> <span data-ttu-id="85afe-160">この chart にもバージョン番号がタグ付けされます。</span><span class="sxs-lookup"><span data-stu-id="85afe-160">The chart is also tagged with a version number.</span></span>
3. <span data-ttu-id="85afe-161">Helm パッケージを Container Registry にプッシュする。</span><span class="sxs-lookup"><span data-stu-id="85afe-161">Push the Helm package to Container Registry.</span></span>

<span data-ttu-id="85afe-162">このビルドが成功したと仮定すると、Azure Pipelines の[リリース パイプライン](/azure/devops/pipelines/release/what-is-release-management)を使用するデプロイ (CD) プロセスがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="85afe-162">Assuming this build succeeds, it triggers a deployment (CD) process using an Azure Pipelines [release pipeline](/azure/devops/pipelines/release/what-is-release-management).</span></span> <span data-ttu-id="85afe-163">パイプラインには次の手順が含まれています。</span><span class="sxs-lookup"><span data-stu-id="85afe-163">This pipeline has the following steps:</span></span>

1. <span data-ttu-id="85afe-164">Helm chart を QA 環境にデプロイする。</span><span class="sxs-lookup"><span data-stu-id="85afe-164">Deploy the Helm chart to a QA environment.</span></span>
1. <span data-ttu-id="85afe-165">パッケージが運用環境に移動される前に、承認者がサインオフする。</span><span class="sxs-lookup"><span data-stu-id="85afe-165">An approver signs off before the package moves to production.</span></span> <span data-ttu-id="85afe-166">「[Release deployment control using approvals (承認を使用したリリース デプロイ制御)](/azure/devops/pipelines/release/approvals/approvals)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="85afe-166">See [Release deployment control using approvals](/azure/devops/pipelines/release/approvals/approvals).</span></span>
1. <span data-ttu-id="85afe-167">Docker イメージに Azure Container Registry 内の運用名前空間のタグを付け直す。</span><span class="sxs-lookup"><span data-stu-id="85afe-167">Retag the Docker image for the production namespace in Azure Container Registry.</span></span> <span data-ttu-id="85afe-168">たとえば、現在のタグが `myrepo.azurecr.io/delivery:v1.0.2` の場合、運用タグは `myrepo.azurecr.io/prod/delivery:v1.0.2` になります。</span><span class="sxs-lookup"><span data-stu-id="85afe-168">For example, if the current tag is `myrepo.azurecr.io/delivery:v1.0.2`, the production tag is `myrepo.azurecr.io/prod/delivery:v1.0.2`.</span></span>
1. <span data-ttu-id="85afe-169">Helm chart を運用環境にデプロイする。</span><span class="sxs-lookup"><span data-stu-id="85afe-169">Deploy the Helm chart to the production environment.</span></span>

<span data-ttu-id="85afe-170">単一リポジトリであっても、これらのタスクのスコープを個々のマイクロサービスに設定できるため、チームは短時間でデプロイすることができます。</span><span class="sxs-lookup"><span data-stu-id="85afe-170">Even in a monorepo, these tasks can be scoped to individual microservices, so that teams can deploy with high velocity.</span></span> <span data-ttu-id="85afe-171">プロセスには、いくつか手動の手順があります。PR の承認、リリース ブランチの作成、および運用クラスターへのデプロイの承認です。</span><span class="sxs-lookup"><span data-stu-id="85afe-171">The process has some manual steps: Approving PRs, creating release branches, and approving deployments into the production cluster.</span></span> <span data-ttu-id="85afe-172">これらの手順はポリシーにより手動で行います。組織が望むのであれば、完全に自動化することもできます。</span><span class="sxs-lookup"><span data-stu-id="85afe-172">These steps are manual by policy &mdash; they could be automated if the organization prefers.</span></span>

## <a name="isolation-of-environments"></a><span data-ttu-id="85afe-173">環境の分離</span><span class="sxs-lookup"><span data-stu-id="85afe-173">Isolation of environments</span></span>

<span data-ttu-id="85afe-174">開発、スモーク テスト、統合テスト、ロード テスト、最後に運用のための環境を含め、サービスをデプロイするための複数の環境が存在します。</span><span class="sxs-lookup"><span data-stu-id="85afe-174">You will have multiple environments where you deploy services, including environments for development, smoke testing, integration testing, load testing, and finally production.</span></span> <span data-ttu-id="85afe-175">これらの環境には、あるレベルの分離が必要です。</span><span class="sxs-lookup"><span data-stu-id="85afe-175">These environments need some level of isolation.</span></span> <span data-ttu-id="85afe-176">Kubernetes では、物理的な分離と論理的な分離のどちらかを選択できます。</span><span class="sxs-lookup"><span data-stu-id="85afe-176">In Kubernetes, you have a choice between physical isolation and logical isolation.</span></span> <span data-ttu-id="85afe-177">物理的な分離は、個別のクラスターにデプロイすることを示します。</span><span class="sxs-lookup"><span data-stu-id="85afe-177">Physical isolation means deploying to separate clusters.</span></span> <span data-ttu-id="85afe-178">論理的な分離では、先に説明したように、名前空間とポリシーを使用します。</span><span class="sxs-lookup"><span data-stu-id="85afe-178">Logical isolation makes use of namespaces and policies, as described earlier.</span></span>

<span data-ttu-id="85afe-179">開発/テスト環境のための個別のクラスターと共に、専用の運用クラスターを作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="85afe-179">Our recommendation is to create a dedicated production cluster along with a separate cluster for your dev/test environments.</span></span> <span data-ttu-id="85afe-180">開発/テスト クラスター内の環境を分離するには、論理的な分離を使用します。</span><span class="sxs-lookup"><span data-stu-id="85afe-180">Use logical isolation to separate environments within the dev/test cluster.</span></span> <span data-ttu-id="85afe-181">開発/テスト クラスターにデプロイされたサービスには、ビジネス データを保持するデータ ストアへのアクセス権を与えるべきではありません。</span><span class="sxs-lookup"><span data-stu-id="85afe-181">Services deployed to the dev/test cluster should never have access to data stores that hold business data.</span></span>

## <a name="build-process"></a><span data-ttu-id="85afe-182">ビルド プロセス</span><span class="sxs-lookup"><span data-stu-id="85afe-182">Build process</span></span>

<span data-ttu-id="85afe-183">可能であれば、ご自分のビルド プロセスを Docker コンテナーにパッケージ化します。</span><span class="sxs-lookup"><span data-stu-id="85afe-183">When possible, package your build process into a Docker container.</span></span> <span data-ttu-id="85afe-184">そうすれば、Docker を使用してご自分のコード成果物をビルドすることができるので、各ビルド コンピューター上でビルド環境を構成しなくても済みます。</span><span class="sxs-lookup"><span data-stu-id="85afe-184">That allows you to build your code artifacts using Docker, without needing to configure the build environment on each build machine.</span></span> <span data-ttu-id="85afe-185">ビルド プロセスのコンテナー化により、新しいビルド エージェントを追加して CI パイプラインをスケール アウトすることが容易になります。</span><span class="sxs-lookup"><span data-stu-id="85afe-185">A containerized build process makes it easy to scale out the CI pipeline by adding new build agents.</span></span> <span data-ttu-id="85afe-186">また、チームの開発者はビルド コンテナーを実行するだけで、コードをビルドできます。</span><span class="sxs-lookup"><span data-stu-id="85afe-186">Also, any developer on the team can build the code simply by running the build container.</span></span>

<span data-ttu-id="85afe-187">Docker 内でマルチ ステージ ビルドを使用すると、単一の Dockerfile 内にビルド環境とランタイム イメージを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="85afe-187">By using multi-stage builds in Docker, you can define the build environment and the runtime image in a single Dockerfile.</span></span> <span data-ttu-id="85afe-188">たとえば、ASP.NET Core アプリケーションをビルドする Dockerfile を次に示します。</span><span class="sxs-lookup"><span data-stu-id="85afe-188">For example, here's a Dockerfile that builds an ASP.NET Core application:</span></span>

```
FROM microsoft/dotnet:2.2-runtime AS base
WORKDIR /app

FROM microsoft/dotnet:2.2-sdk AS build
WORKDIR /src/Fabrikam.Workflow.Service

COPY Fabrikam.Workflow.Service/Fabrikam.Workflow.Service.csproj .
RUN dotnet restore Fabrikam.Workflow.Service.csproj

COPY Fabrikam.Workflow.Service/. .
RUN dotnet build Fabrikam.Workflow.Service.csproj -c Release -o /app

FROM build AS publish
RUN dotnet publish Fabrikam.Workflow.Service.csproj -c Release -o /app

FROM base AS final
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "Fabrikam.Workflow.Service.dll"]
```

<span data-ttu-id="85afe-189">&#11162; [ソース ファイル](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/workflow/Dockerfile)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="85afe-189">&#11162; See the [source file](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/workflow/Dockerfile).</span></span>

<span data-ttu-id="85afe-190">この Dockerfile では、いくつかのビルド ステージが定義されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-190">This Dockerfile defines several build stages.</span></span> <span data-ttu-id="85afe-191">`base` という名前のステージでは ASP.NET Core ランタイムが使用されていますが、`build` という名前のステージでは完全な ASP.NET Core SDK が使用されていることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="85afe-191">Notice that the stage named `base` uses the ASP.NET Core runtime, while the stage named `build` uses the full ASP.NET Core SDK.</span></span> <span data-ttu-id="85afe-192">`build` ステージは、ASP.NET Core プロジェクトをビルドするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-192">The `build` stage is used to build the ASP.NET Core project.</span></span> <span data-ttu-id="85afe-193">しかし、最終的なランタイム コンテナーは `base` から作成されます。これは、ランタイムしか含まないので、完全な SDK イメージよりかなり小さくなっています。</span><span class="sxs-lookup"><span data-stu-id="85afe-193">But the final runtime container is built from `base`, which contains just the runtime and is significantly smaller than the full SDK image.</span></span>

### <a name="building-a-test-runner"></a><span data-ttu-id="85afe-194">テスト ランナーのビルド</span><span class="sxs-lookup"><span data-stu-id="85afe-194">Building a test runner</span></span>

<span data-ttu-id="85afe-195">もう 1 つのおすすめできる方法は、コンテナー内で単体テストを実行することです。</span><span class="sxs-lookup"><span data-stu-id="85afe-195">Another good practice is to run unit tests in the container.</span></span> <span data-ttu-id="85afe-196">たとえば、次に示すのは、テスト ランナーをビルドする Docker ファイルの一部です。</span><span class="sxs-lookup"><span data-stu-id="85afe-196">For example, here is part of a Docker file that builds a test runner:</span></span>

```
FROM build AS testrunner
WORKDIR /src/tests

COPY Fabrikam.Workflow.Service.Tests/*.csproj .
RUN dotnet restore Fabrikam.Workflow.Service.Tests.csproj

COPY Fabrikam.Workflow.Service.Tests/. .
ENTRYPOINT ["dotnet", "test", "--logger:trx"]
```

<span data-ttu-id="85afe-197">開発者は、この Docker ファイルを使用することで、テストをローカルで実行することができます。</span><span class="sxs-lookup"><span data-stu-id="85afe-197">A developer can use this Docker file to run the tests locally:</span></span>

```bash
docker build . -t delivery-test:1 --target=testrunner
docker run -p 8080:8080 delivery-test:1
```

<span data-ttu-id="85afe-198">また、CI パイプラインでは、ビルド検証手順の一環としてテストが実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="85afe-198">The CI pipeline should also run the tests as part of the build verification step.</span></span>

<span data-ttu-id="85afe-199">このファイルでは Docker の `RUN` コマンドではなく、Docker の `ENTRYPOINT` コマンドが使用されていることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="85afe-199">Note that this file uses the Docker `ENTRYPOINT` command to run the tests, not the Docker `RUN` command.</span></span>

- <span data-ttu-id="85afe-200">`RUN` コマンドを使用した場合は、イメージをビルドするたびにテストが実行されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-200">If you use the `RUN` command, the tests run every time you build the image.</span></span> <span data-ttu-id="85afe-201">`ENTRYPOINT` を使用することにより、テストはオプトインになります。</span><span class="sxs-lookup"><span data-stu-id="85afe-201">By using `ENTRYPOINT`, the tests are opt-in.</span></span> <span data-ttu-id="85afe-202">`testrunner` ステージを明示的にターゲットとした場合に限り、テストが実行されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-202">They run only when you explicitly target the `testrunner` stage.</span></span>
- <span data-ttu-id="85afe-203">テストの失敗により、Docker の `build` コマンドが失敗することはありません。</span><span class="sxs-lookup"><span data-stu-id="85afe-203">A failing test doesn't cause the Docker `build` command to fail.</span></span> <span data-ttu-id="85afe-204">このようにして、コンテナー ビルドの失敗をテストの失敗と区別することができます。</span><span class="sxs-lookup"><span data-stu-id="85afe-204">That way you can distinguish container build failures from test failures.</span></span>
- <span data-ttu-id="85afe-205">テストの結果は、マウントされたボリュームに保存できます。</span><span class="sxs-lookup"><span data-stu-id="85afe-205">Test results can be saved to a mounted volume.</span></span>

### <a name="container-best-practices"></a><span data-ttu-id="85afe-206">コンテナーのベスト プラクティス</span><span class="sxs-lookup"><span data-stu-id="85afe-206">Container best practices</span></span>

<span data-ttu-id="85afe-207">コンテナーについて考慮すべきその他のベスト プラクティスを次に示します。</span><span class="sxs-lookup"><span data-stu-id="85afe-207">Here are some other best practices to consider for containers:</span></span>

- <span data-ttu-id="85afe-208">クラスター (ポッドやサービスなど) にデプロイされるリソースのコンテナー タグ、バージョン管理、および名前付けに関する組織規模の規則を定義します。</span><span class="sxs-lookup"><span data-stu-id="85afe-208">Define organization-wide conventions for container tags, versioning, and naming conventions for resources deployed to the cluster (pods, services, and so on).</span></span> <span data-ttu-id="85afe-209">これにより、デプロイに関する問題が診断しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="85afe-209">That can make it easier to diagnose deployment issues.</span></span>

- <span data-ttu-id="85afe-210">開発とテストのサイクル中、CI/CD 処理は、多数のコンテナー イメージをビルドします。</span><span class="sxs-lookup"><span data-stu-id="85afe-210">During the development and test cycle, the CI/CD process will build many container images.</span></span> <span data-ttu-id="85afe-211">それらのイメージの一部のみがリリース候補になり、さらにそれらのリリース候補の一部のみが運用環境に昇格されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-211">Only some of those images are candidates for release, and then only some of those release candidates will get promoted to production.</span></span> <span data-ttu-id="85afe-212">明確なバージョン管理戦略を用意してください。それによって、どのバージョンが現在運用環境にデプロイされているかを把握し、必要な場合に前のバージョンにロールバックすることができます。</span><span class="sxs-lookup"><span data-stu-id="85afe-212">Have a clear versioning strategy, so that you know which images are currently deployed to production, and can roll back to a previous version if necessary.</span></span>

- <span data-ttu-id="85afe-213">`latest` ではなく、特定のバージョン タグを常にデプロイしてください。</span><span class="sxs-lookup"><span data-stu-id="85afe-213">Always deploy specific container version tags, not `latest`.</span></span>

- <span data-ttu-id="85afe-214">Azure Container Registry 内の[名前空間](/azure/container-registry/container-registry-best-practices#repository-namespaces)を使用して、運用するために承認されたイメージを、まだテスト中であるイメージから分離します。</span><span class="sxs-lookup"><span data-stu-id="85afe-214">Use [namespaces](/azure/container-registry/container-registry-best-practices#repository-namespaces) in Azure Container Registry to isolate images that are approved for production from images that are still being tested.</span></span> <span data-ttu-id="85afe-215">運用環境にデプロイする準備ができるまで、イメージを運用名前空間に移動しないでください。</span><span class="sxs-lookup"><span data-stu-id="85afe-215">Don't move an image into the production namespace until you're ready to deploy it into production.</span></span> <span data-ttu-id="85afe-216">このプラクティスとコンテナー イメージのセマンティックなバージョン管理を組み合わせると、リリースを承認されていないバージョンが誤ってデプロイされる可能性を低くすることができます。</span><span class="sxs-lookup"><span data-stu-id="85afe-216">If you combine this practice with semantic versioning of container images, it can reduce the chance of accidentally deploying a version that wasn't approved for release.</span></span>

- <span data-ttu-id="85afe-217">コンテナーを特権のないユーザーとして実行して、最小特権の原則に従います。</span><span class="sxs-lookup"><span data-stu-id="85afe-217">Follow the principle of least privilege by running containers as a nonprivileged user.</span></span> <span data-ttu-id="85afe-218">Kubernetes では、コンテナーが "*ルート*" として実行されないように、ポッド セキュリティ ポリシーを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="85afe-218">In Kubernetes, you can create a pod security policy that prevents containers from running as *root*.</span></span> <span data-ttu-id="85afe-219">[ポッドがルート権限で実行されるのを防止](https://docs.bitnami.com/kubernetes/how-to/secure-kubernetes-cluster-psp/)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="85afe-219">See [Prevent Pods From Running With Root Privileges](https://docs.bitnami.com/kubernetes/how-to/secure-kubernetes-cluster-psp/).</span></span>

## <a name="helm-charts"></a><span data-ttu-id="85afe-220">Helm チャート</span><span class="sxs-lookup"><span data-stu-id="85afe-220">Helm charts</span></span>

<span data-ttu-id="85afe-221">Helm を使用してサービスの構築やデプロイを管理することを考慮してください。</span><span class="sxs-lookup"><span data-stu-id="85afe-221">Consider using Helm to manage building and deploying services.</span></span> <span data-ttu-id="85afe-222">CI/CD に役立つ Helm の機能のいくつかを次に示します。</span><span class="sxs-lookup"><span data-stu-id="85afe-222">Here are some of the features of Helm that help with CI/CD:</span></span>

- <span data-ttu-id="85afe-223">多くの場合、単一のマイクロサービスは、複数の Kubernetes オブジェクトによって定義されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-223">Often a single microservice is defined by multiple Kubernetes objects.</span></span> <span data-ttu-id="85afe-224">Helm を使用すると、これらのオブジェクトを 1 つの Helm chart にパッケージ化できます。</span><span class="sxs-lookup"><span data-stu-id="85afe-224">Helm allows these objects to be packaged into a single Helm chart.</span></span>
- <span data-ttu-id="85afe-225">一連の kubectl コマンドではなく、1 つの Helm コマンドを使用して chart をデプロイできます。</span><span class="sxs-lookup"><span data-stu-id="85afe-225">A chart can be deployed with a single Helm command, rather than a series of kubectl commands.</span></span>
- <span data-ttu-id="85afe-226">グラフは明示的にバージョン管理されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-226">Charts are explicitly versioned.</span></span> <span data-ttu-id="85afe-227">Helm を使用して、バージョンのリリース、リリースの表示、および以前のバージョンへのロールバックを行います。</span><span class="sxs-lookup"><span data-stu-id="85afe-227">Use Helm to release a version, view releases, and roll back to a previous version.</span></span> <span data-ttu-id="85afe-228">以前のバージョンにロールバックする機能と共にセマンティック バージョニングを使用して、更新プログラムやリビジョンを追跡します。</span><span class="sxs-lookup"><span data-stu-id="85afe-228">Tracking updates and revisions, using semantic versioning, along with the ability to roll back to a previous version.</span></span>
- <span data-ttu-id="85afe-229">Helm chart では、多数のファイルにわたる情報の複製 (ラベルやセレクターなど) を回避するためにテンプレートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-229">Helm charts use templates to avoid duplicating information, such as labels and selectors, across many files.</span></span>
- <span data-ttu-id="85afe-230">Helm では、chart 間の依存関係を管理できます。</span><span class="sxs-lookup"><span data-stu-id="85afe-230">Helm can manage dependencies between charts.</span></span>
- <span data-ttu-id="85afe-231">chart を Helm リポジトリ (Azure Container Registry など) に格納して、ビルド パイプラインに統合することができます。</span><span class="sxs-lookup"><span data-stu-id="85afe-231">Charts can be stored in a Helm repository, such as Azure Container Registry, and integrated into the build pipeline.</span></span>

<span data-ttu-id="85afe-232">Helm リポジトリとしての Container Registry の使用の詳細については、「[アプリケーションのグラフに Helm リポジトリとして Azure Container Registry を使用する](/azure/container-registry/container-registry-helm-repos)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="85afe-232">For more information about using Container Registry as a Helm repository, see [Use Azure Container Registry as a Helm repository for your application charts](/azure/container-registry/container-registry-helm-repos).</span></span>

<span data-ttu-id="85afe-233">1 つのマイクロサービスに、複数の Kubernetes 構成ファイルが必要となる場合があります。</span><span class="sxs-lookup"><span data-stu-id="85afe-233">A single microservice may involve multiple Kubernetes configuration files.</span></span> <span data-ttu-id="85afe-234">サービスの更新は、セレクター、ラベル、およびイメージのタグを更新するためにこれらのファイルに手を加えることを意味する場合があります。</span><span class="sxs-lookup"><span data-stu-id="85afe-234">Updating a service can mean touching all of these files to update selectors, labels, and image tags.</span></span> <span data-ttu-id="85afe-235">Helm では、これらが chart と呼ばれる 1 つのパッケージとして扱われ、変数を使用することで YAML ファイルを簡単に更新することができます。</span><span class="sxs-lookup"><span data-stu-id="85afe-235">Helm treats these as a single package called a chart and allows you to easily update the YAML files by using variables.</span></span> <span data-ttu-id="85afe-236">Helm では、パラメーター化された YAML 構成ファイルを記述できるようにするために、(Go テンプレートに基づく) テンプレート言語が使用されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-236">Helm uses a template language (based on Go templates) to let you write parameterized YAML configuration files.</span></span>

<span data-ttu-id="85afe-237">たとえば、デプロイを定義する YAML ファイルの一部を次に示します。</span><span class="sxs-lookup"><span data-stu-id="85afe-237">For example, here's part of a YAML file that defines a deployment:</span></span>

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "package.fullname" . | replace "." "" }}
  labels:
    app.kubernetes.io/name: {{ include "package.name" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
  annotations:
    kubernetes.io/change-cause: {{ .Values.reason }}

...

  spec:
      containers:
      - name: &package-container_name fabrikam-package
        image: {{ .Values.dockerregistry }}/{{ .Values.image.repository }}:{{ .Values.image.tag }}
        imagePullPolicy: {{ .Values.image.pullPolicy }}
        env:
        - name: LOG_LEVEL
          value: {{ .Values.log.level }}
```

<span data-ttu-id="85afe-238">&#11162; [ソース ファイル](https://github.com/mspnp/microservices-reference-implementation/blob/master/charts/package/templates/package-deploy.yaml)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="85afe-238">&#11162; See the [source file](https://github.com/mspnp/microservices-reference-implementation/blob/master/charts/package/templates/package-deploy.yaml).</span></span>

<span data-ttu-id="85afe-239">デプロイ名、ラベル、およびコンテナー仕様のすべてで、テンプレート パラメーターが使用されていることがわかります。これらはデプロイ時に指定されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-239">You can see that the deployment name, labels, and container spec all use template parameters, which are provided at deployment time.</span></span> <span data-ttu-id="85afe-240">たとえば、コマンドラインから次を実行します。</span><span class="sxs-lookup"><span data-stu-id="85afe-240">For example, from the command line:</span></span>

```bash
helm install $HELM_CHARTS/package/ \
     --set image.tag=0.1.0 \
     --set image.repository=package \
     --set dockerregistry=$ACR_SERVER \
     --namespace backend \
     --name package-v0.1.0
```

<span data-ttu-id="85afe-241">ご利用の CI/CD パイプラインで、直接、Kubernetes に chart をインストール可能であるとしても、chart アーカイブ (.tgz ファイル) を作成して、chart を Azure Container Registry などの Helm リポジトリにプッシュすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="85afe-241">Although your CI/CD pipeline could install a chart directly to Kubernetes, we recommend creating a chart archive (.tgz file) and pushing the chart to a Helm repository such as Azure Container Registry.</span></span> <span data-ttu-id="85afe-242">詳細については、「[Package Docker-based apps in Helm charts in Azure Pipelines](/azure/devops/pipelines/languages/helm?view=azure-devops)」 (Azure パイプライン内で Docker ベースのアプリを Helm charts にパッケージ化する) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="85afe-242">For more information, see [Package Docker-based apps in Helm charts in Azure Pipelines](/azure/devops/pipelines/languages/helm?view=azure-devops).</span></span>

<span data-ttu-id="85afe-243">Helm を独自の名前空間にデプロイすること、およびロールベースのアクセス制御 (RBAC) を使用して Helm をデプロイできる名前空間を制限することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="85afe-243">Consider deploying Helm to its own namespace and using role-based access control (RBAC) to restrict which namespaces it can deploy to.</span></span> <span data-ttu-id="85afe-244">詳細については、Helm のドキュメント内の[ロールベースのアクセス制御](https://helm.sh/docs/using_helm/#helm-and-role-based-access-control)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="85afe-244">For more information, see [Role-based Access Control](https://helm.sh/docs/using_helm/#helm-and-role-based-access-control) in the Helm documentation.</span></span>

### <a name="revisions"></a><span data-ttu-id="85afe-245">リビジョン</span><span class="sxs-lookup"><span data-stu-id="85afe-245">Revisions</span></span>

<span data-ttu-id="85afe-246">Helm chart には常にバージョン番号が用意されます。これには[セマンティック バージョニング](https://semver.org/)が使用される必要があります。</span><span class="sxs-lookup"><span data-stu-id="85afe-246">Helm charts always have a version number, which must use [semantic versioning](https://semver.org/).</span></span> <span data-ttu-id="85afe-247">chart には `appVersion` も用意されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-247">A chart can also have an `appVersion`.</span></span> <span data-ttu-id="85afe-248">このフィールドはオプションであり、chart バージョンに関連付ける必要はありません。</span><span class="sxs-lookup"><span data-stu-id="85afe-248">This field is optional, and doesn't have to be related to the chart version.</span></span> <span data-ttu-id="85afe-249">チームによっては、chart に対する更新プログラムとは別にアプリケーション バージョンを必要とする場合があります。</span><span class="sxs-lookup"><span data-stu-id="85afe-249">Some teams might want to application versions separately from updates to the charts.</span></span> <span data-ttu-id="85afe-250">しかし、よりシンプルな方法は、1 つのバージョン番号を使用することです。結果、chart バージョンとアプリケーション バージョンの間の関係は 1:1 になります。</span><span class="sxs-lookup"><span data-stu-id="85afe-250">But a simpler approach is to use one version number, so there's a 1:1 relation between chart version and application version.</span></span> <span data-ttu-id="85afe-251">そうすれば、リリースごとに 1 つの chart を格納して、必要なリリースを簡単にデプロイすることができます。</span><span class="sxs-lookup"><span data-stu-id="85afe-251">That way, you can store one chart per release and easily deploy the desired release:</span></span>

```bash
helm install <package-chart-name> --version <desiredVersion>
```

<span data-ttu-id="85afe-252">もう 1 つ、次のようにデプロイ テンプレート内に change-cause 注釈を指定することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="85afe-252">Another good practice is to provide a change-cause annotation in the deployment template:</span></span>

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "delivery.fullname" . | replace "." "" }}
  labels:
     ...
  annotations:
    kubernetes.io/change-cause: {{ .Values.reason }}
```

<span data-ttu-id="85afe-253">これにより、`kubectl rollout history` コマンドを使用して、リビジョンごとに change-cause フィールドを表示することができます。</span><span class="sxs-lookup"><span data-stu-id="85afe-253">This lets you view the change-cause field for each revision, using the `kubectl rollout history` command.</span></span> <span data-ttu-id="85afe-254">前の例では、change-cause は Helm chart のパラメーターとして指定されました。</span><span class="sxs-lookup"><span data-stu-id="85afe-254">In the previous example, the change-cause is provided as a Helm chart parameter.</span></span>

```bash
$ kubectl rollout history deployments/delivery-v010 -n backend
deployment.extensions/delivery-v010
REVISION  CHANGE-CAUSE
1         Initial deployment
```

<span data-ttu-id="85afe-255">`helm list` コマンドを使用して、次のようにリビジョン履歴を表示することもできます。</span><span class="sxs-lookup"><span data-stu-id="85afe-255">You can also use the `helm list` command to view the revision history:</span></span>

```bash
~$ helm list
NAME            REVISION    UPDATED                     STATUS        CHART            APP VERSION     NAMESPACE
delivery-v0.1.0 1           Sun Apr  7 00:25:30 2019    DEPLOYED      delivery-v0.1.0  v0.1.0          backend
```

> [!TIP]
> <span data-ttu-id="85afe-256">Helm を初期化する際は、`--history-max` フラグを使用します。</span><span class="sxs-lookup"><span data-stu-id="85afe-256">Use the `--history-max` flag when initializing Helm.</span></span> <span data-ttu-id="85afe-257">この設定により、Tiller がその履歴内に保存するリビジョン数が制限されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-257">This setting limits the number of revisions that Tiller saves in its history.</span></span> <span data-ttu-id="85afe-258">Tiller では configmaps 内にリビジョン履歴が格納されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-258">Tiller stores revision history in configmaps.</span></span> <span data-ttu-id="85afe-259">更新プログラムを頻繁にリリースしている場合、履歴のサイズを制限しないと、configmaps のサイズは非常に大きくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85afe-259">If you're releasing updates frequently, the configmaps can grow very large unless you limit the history size.</span></span>

## <a name="azure-devops-pipeline"></a><span data-ttu-id="85afe-260">Azure DevOps パイプライン</span><span class="sxs-lookup"><span data-stu-id="85afe-260">Azure DevOps Pipeline</span></span>

<span data-ttu-id="85afe-261">Azure Pipeline では、パイプラインは*ビルド パイプライン*と*リリース パイプライン*に分割されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-261">In Azure Pipelines, pipelines are divided into *build pipelines* and *release pipelines*.</span></span> <span data-ttu-id="85afe-262">ビルド パイプラインでは、CI プロセスが実行され、ビルド成果物が作成されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-262">The build pipeline runs the CI process and creates build artifacts.</span></span> <span data-ttu-id="85afe-263">Kubernetes 上のマイクロサービス アーキテクチャの場合、これらの成果物は、各マイクロサービスを定義するコンテナー イメージと Helm chart です。</span><span class="sxs-lookup"><span data-stu-id="85afe-263">For a microservices architecture on Kubernetes, these artifacts are the container images and Helm charts that define each microservice.</span></span> <span data-ttu-id="85afe-264">リリース パイプラインでは、その CD プロセスが実行され、それによってマイクロサービスがクラスターにデプロイされます。</span><span class="sxs-lookup"><span data-stu-id="85afe-264">The release pipeline runs that CD process that deploys a microservice into a cluster.</span></span>

<span data-ttu-id="85afe-265">この記事で前述した CI フローに基づいて、ビルド パイプラインは次のタスクで構成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85afe-265">Based on the CI flow described earlier in this article, a build pipeline might consist of the following tasks:</span></span>

1. <span data-ttu-id="85afe-266">テスト ランナー コンテナーをビルドします。</span><span class="sxs-lookup"><span data-stu-id="85afe-266">Build the test runner container.</span></span>

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        arguments: '--pull --target testrunner'
        dockerFile: $(System.DefaultWorkingDirectory)/$(dockerFileName)
        imageName: '$(imageName)-test'
    ```

1. <span data-ttu-id="85afe-267">テスト ランナー コンテナーに対して docker run を呼び出すことによって、テストを実行します。</span><span class="sxs-lookup"><span data-stu-id="85afe-267">Run the tests, by invoking docker run against the test runner container.</span></span>

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        command: 'run'
        containerName: testrunner
        volumes: '$(System.DefaultWorkingDirectory)/TestResults:/app/tests/TestResults'
        imageName: '$(imageName)-test'
        runInBackground: false
    ```

1. <span data-ttu-id="85afe-268">テスト結果を公開します。</span><span class="sxs-lookup"><span data-stu-id="85afe-268">Publish the test results.</span></span> <span data-ttu-id="85afe-269">「[Integrate build and test tasks](/azure/devops/pipelines/languages/docker?view=azure-devops&tabs=yaml#integrate-build-and-test-tasks)」 (タスクの統合、ビルドおよびテスト) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="85afe-269">See [Integrate build and test tasks](/azure/devops/pipelines/languages/docker?view=azure-devops&tabs=yaml#integrate-build-and-test-tasks).</span></span>

    ```yaml
    - task: PublishTestResults@2
      inputs:
        testResultsFormat: 'VSTest'
        testResultsFiles: 'TestResults/*.trx'
        searchFolder: '$(System.DefaultWorkingDirectory)'
        publishRunAttachments: true
    ```

1. <span data-ttu-id="85afe-270">ランタイム コンテナーをビルドします。</span><span class="sxs-lookup"><span data-stu-id="85afe-270">Build the runtime container.</span></span>

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        dockerFile: $(System.DefaultWorkingDirectory)/$(dockerFileName)
        includeLatestTag: false
        imageName: '$(imageName)'
    ```

1. <span data-ttu-id="85afe-271">Azure Container Registry (またはその他のコンテナー レジストリ) にコンテナーをプッシュします。</span><span class="sxs-lookup"><span data-stu-id="85afe-271">Push to the container to Azure Container Registry (or other container registry).</span></span>

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        command: 'Push an image'
        imageName: '$(imageName)'
        includeSourceTags: false
    ```

1. <span data-ttu-id="85afe-272">Helm chart をパッケージ化します。</span><span class="sxs-lookup"><span data-stu-id="85afe-272">Package the Helm chart.</span></span>

    ```yaml
    - task: HelmDeploy@0
      inputs:
        command: package
        chartPath: $(chartPath)
        chartVersion: $(Build.SourceBranchName)
        arguments: '--app-version $(Build.SourceBranchName)'
    ```

1. <span data-ttu-id="85afe-273">Azure Container Registry (またはその他の Helm リポジトリ) に Helm パッケージをプッシュします。</span><span class="sxs-lookup"><span data-stu-id="85afe-273">Push the Helm package to Azure Container Registry (or other Helm repository).</span></span>

    ```yaml
    task: AzureCLI@1
      inputs:
        azureSubscription: $(AzureSubscription)
        scriptLocation: inlineScript
        inlineScript: |
        az acr helm push $(System.ArtifactsDirectory)/$(repositoryName)-$(Build.SourceBranchName).tgz --name $(AzureContainerRegistry);
    ```

<span data-ttu-id="85afe-274">&#11162; [ソース ファイル](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-ci.yml)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="85afe-274">&#11162; See the [source file](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-ci.yml).</span></span>

<span data-ttu-id="85afe-275">CI パイプラインからの出力は、実稼働可能なコンテナー イメージと、マイクロサービスの更新された Helm chart です。</span><span class="sxs-lookup"><span data-stu-id="85afe-275">The output from the CI pipeline is a production-ready container image and an updated Helm chart for the microservice.</span></span> <span data-ttu-id="85afe-276">この時点では、リリース パイプラインに引き継ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="85afe-276">At this point, the release pipeline can take over.</span></span> <span data-ttu-id="85afe-277">それによって次の手順が実行されます。</span><span class="sxs-lookup"><span data-stu-id="85afe-277">It performs the following steps:</span></span>

- <span data-ttu-id="85afe-278">開発、QA、ステージング環境にデプロイする。</span><span class="sxs-lookup"><span data-stu-id="85afe-278">Deploy to dev/QA/staging environments.</span></span>
- <span data-ttu-id="85afe-279">承認者がデプロイを承認または却下するのを待機する。</span><span class="sxs-lookup"><span data-stu-id="85afe-279">Wait for an approver to approve or reject the deployment.</span></span>
- <span data-ttu-id="85afe-280">コンテナー イメージにリリースのためのタグを付け直す</span><span class="sxs-lookup"><span data-stu-id="85afe-280">Retag the container image for release</span></span>
- <span data-ttu-id="85afe-281">リリース タグをコンテナー レジストリにプッシュする。</span><span class="sxs-lookup"><span data-stu-id="85afe-281">Push the release tag to the container registry.</span></span>
- <span data-ttu-id="85afe-282">運用環境クラスター内の Helm chart をアップグレードする。</span><span class="sxs-lookup"><span data-stu-id="85afe-282">Upgrade the Helm chart in the production cluster.</span></span>

<span data-ttu-id="85afe-283">リリース パイプラインの作成の詳細については、「[Release pipelines, draft releases, and release options](/azure/devops/pipelines/release/?view=azure-devops)」 (リリース パイプライン、ドラフト リリース、およびリリース オプション) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="85afe-283">For more information about creating a release pipeline, see [Release pipelines, draft releases, and release options](/azure/devops/pipelines/release/?view=azure-devops).</span></span>

<span data-ttu-id="85afe-284">次の図に、この記事で説明したエンドツーエンドの CI/CD プロセスを示します。</span><span class="sxs-lookup"><span data-stu-id="85afe-284">The following diagram shows the end-to-end CI/CD process described in this article:</span></span>

![CD/CD パイプライン](./images/aks-cicd-flow.png)

## <a name="next-steps"></a><span data-ttu-id="85afe-286">次の手順</span><span class="sxs-lookup"><span data-stu-id="85afe-286">Next steps</span></span>

<span data-ttu-id="85afe-287">この記事は、[GitHub][ri] 上で確認できる参照実装に基づいています。</span><span class="sxs-lookup"><span data-stu-id="85afe-287">This article was based on a reference implementation that you can find on [GitHub][ri].</span></span>

[ri]: https://github.com/mspnp/microservices-reference-implementation