---
title: Azure アプリケーションの回復性と可用性のテスト
description: Azure のアプリケーションの信頼性をテストする方法
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: cf33a859540810279b1cb85be5a6fb2ebe4500dc
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59642875"
---
# <a name="testing-azure-applications-for-resiliency-and-availability"></a><span data-ttu-id="d9178-103">Azure アプリケーションの回復性と可用性のテスト</span><span class="sxs-lookup"><span data-stu-id="d9178-103">Testing Azure applications for resiliency and availability</span></span>

<span data-ttu-id="d9178-104">回復性をテストするには、断続的に障害が発生する状況下でエンドツーエンドのワークロードがどのように実行されるかを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d9178-104">To test resiliency, you should verify how the end-to-end workload performs under intermittent failure conditions.</span></span>

<span data-ttu-id="d9178-105">人工的なユーザー データと実際のユーザー データの両方を使用して、運用環境でテストを実行します。</span><span class="sxs-lookup"><span data-stu-id="d9178-105">Run tests in production using both synthetic and real user data.</span></span> <span data-ttu-id="d9178-106">テストと運用はほとんど同じですので、[Blue/Green](https://martinfowler.com/bliki/BlueGreenDeployment.html) または[カナリア デプロイ](https://martinfowler.com/bliki/CanaryRelease.html)を使用し、運用環境でアプリケーションを検証することが重要です。</span><span class="sxs-lookup"><span data-stu-id="d9178-106">Test and production are rarely identical, so it's important to validate your application in production using a [blue-green](https://martinfowler.com/bliki/BlueGreenDeployment.html) or [canary deployment](https://martinfowler.com/bliki/CanaryRelease.html).</span></span> <span data-ttu-id="d9178-107">これにより、実際の条件でアプリケーションをテストし、完全にデプロイされているときにアプリケーションが予期したとおりに動作することを確認できます。</span><span class="sxs-lookup"><span data-stu-id="d9178-107">This way, you're testing the application under real conditions, so you can be sure that it will function as expected when fully deployed.</span></span>

<span data-ttu-id="d9178-108">テスト計画の一環として、次を含めます。</span><span class="sxs-lookup"><span data-stu-id="d9178-108">As part of your test plan, include:</span></span>

- <span data-ttu-id="d9178-109">自動デプロイ前テスト</span><span class="sxs-lookup"><span data-stu-id="d9178-109">Automated predeployment testing</span></span>
- <span data-ttu-id="d9178-110">フォールト挿入テスト</span><span class="sxs-lookup"><span data-stu-id="d9178-110">Fault injection testing</span></span>
- <span data-ttu-id="d9178-111">ピーク ロード テスト</span><span class="sxs-lookup"><span data-stu-id="d9178-111">Peak load testing</span></span>
- <span data-ttu-id="d9178-112">ディザスター リカバリー テスト</span><span class="sxs-lookup"><span data-stu-id="d9178-112">Disaster recovery testing</span></span>
- <span data-ttu-id="d9178-113">サードパーティー サービス テスト</span><span class="sxs-lookup"><span data-stu-id="d9178-113">Third-party service testing</span></span>

<span data-ttu-id="d9178-114">テストは反復的なプロセスです。</span><span class="sxs-lookup"><span data-stu-id="d9178-114">Testing is an iterative process.</span></span> <span data-ttu-id="d9178-115">アプリケーションをテストし、結果を測定し、結果のすべてのエラーを分析して対応し、プロセスを繰り返します。</span><span class="sxs-lookup"><span data-stu-id="d9178-115">Test the application, measure the outcome, analyze and address any failures that result, and repeat the process.</span></span>

## <a name="perform-fault-injection-testing"></a><span data-ttu-id="d9178-116">フォールト挿入テストを実行する</span><span class="sxs-lookup"><span data-stu-id="d9178-116">Perform fault injection testing</span></span>

<span data-ttu-id="d9178-117">フォールト挿入テストでは、実際の障害をトリガーするまたはシミュレートすることによって、システムの障害への回復性を確認します。</span><span class="sxs-lookup"><span data-stu-id="d9178-117">For fault injection testing, check the resiliency of the system during failures, either by triggering actual failures or by simulating them.</span></span> <span data-ttu-id="d9178-118">次のような方法で障害を再現します。</span><span class="sxs-lookup"><span data-stu-id="d9178-118">Here are some strategies to induce failures:</span></span>

- <span data-ttu-id="d9178-119">仮想マシン (VM) インスタンスのシャットダウン。</span><span class="sxs-lookup"><span data-stu-id="d9178-119">Shut down virtual machine (VM) instances.</span></span>
- <span data-ttu-id="d9178-120">プロセスのクラッシュ。</span><span class="sxs-lookup"><span data-stu-id="d9178-120">Crash processes.</span></span>
- <span data-ttu-id="d9178-121">証明書の期限切れ。</span><span class="sxs-lookup"><span data-stu-id="d9178-121">Expire certificates.</span></span>
- <span data-ttu-id="d9178-122">アクセス キーの変更。</span><span class="sxs-lookup"><span data-stu-id="d9178-122">Change access keys.</span></span>
- <span data-ttu-id="d9178-123">ドメイン コントローラー上の DNS サービスのシャットダウン。</span><span class="sxs-lookup"><span data-stu-id="d9178-123">Shut down the DNS service on domain controllers.</span></span>
- <span data-ttu-id="d9178-124">RAM やスレッド数など、使用可能なシステム リソースの制限。</span><span class="sxs-lookup"><span data-stu-id="d9178-124">Limit available system resources, such as RAM or number of threads.</span></span>
- <span data-ttu-id="d9178-125">ディスクのマウント解除。</span><span class="sxs-lookup"><span data-stu-id="d9178-125">Unmount disks.</span></span>
- <span data-ttu-id="d9178-126">VM の再デプロイ。</span><span class="sxs-lookup"><span data-stu-id="d9178-126">Redeploy a VM.</span></span>

<span data-ttu-id="d9178-127">テスト計画には、一般的な障害のシナリオだけでなく、デザイン フェーズ中に特定される可能性がある障害点を組み込む必要があります。</span><span class="sxs-lookup"><span data-stu-id="d9178-127">Your test plan should incorporate possible failure points identified during the design phase, in addition to common failure scenarios:</span></span>

- <span data-ttu-id="d9178-128">アプリケーションは可能な限り運用に近い環境でテストします。</span><span class="sxs-lookup"><span data-stu-id="d9178-128">Test your application in an environment as close to production as possible.</span></span>
- <span data-ttu-id="d9178-129">障害を組み合わせてテストします。</span><span class="sxs-lookup"><span data-stu-id="d9178-129">Test failures in combination.</span></span>
- <span data-ttu-id="d9178-130">回復時間を測定し、ビジネス要件を満たしていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d9178-130">Measure the recovery times, and be sure that your business requirements are met.</span></span>
- <span data-ttu-id="d9178-131">障害が連鎖しないこと、分離された方法で処理されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d9178-131">Verify that failures don't cascade and are handled in an isolated way.</span></span>

<span data-ttu-id="d9178-132">障害のシナリオの詳細については、「[Failure and disaster recovery for Azure applications](./disaster-recovery.md)」 (Azure アプリケーションの障害とディザスター リカバリー) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d9178-132">For more information about failure scenarios, see [Failure and disaster recovery for Azure applications](./disaster-recovery.md).</span></span>

## <a name="test-under-peak-loads"></a><span data-ttu-id="d9178-133">ピーク負荷でテストする</span><span class="sxs-lookup"><span data-stu-id="d9178-133">Test under peak loads</span></span>

<span data-ttu-id="d9178-134">ロード テストは、過負荷状態またはサービスの調整が行われているバックエンド データベースなど、負荷がかかった状態でのみ発生する障害を特定するために重要です。</span><span class="sxs-lookup"><span data-stu-id="d9178-134">Load testing is crucial for identifying failures that only happen under load, such as the back-end database being overwhelmed or service throttling.</span></span> <span data-ttu-id="d9178-135">運用データまたは運用データに可能な限り近い人工的なデータを使用して、ピーク負荷と、ピーク負荷で予想される増加をテストします。</span><span class="sxs-lookup"><span data-stu-id="d9178-135">Test for peak load and anticipated increase in peak load, using production data or synthetic data that is as close to production data as possible.</span></span> <span data-ttu-id="d9178-136">目標は、実際の条件下でアプリケーションがどのように動作するかを確認することです。</span><span class="sxs-lookup"><span data-stu-id="d9178-136">Your goal is to see how the application behaves under real-world conditions.</span></span>

## <a name="conduct-disaster-recovery-drills"></a><span data-ttu-id="d9178-137">ディザスター リカバリー訓練を実施する</span><span class="sxs-lookup"><span data-stu-id="d9178-137">Conduct disaster recovery drills</span></span>

<span data-ttu-id="d9178-138">適切なディザスター リカバリー計画から開始し、それを定期的にテストして、機能することを確認します。</span><span class="sxs-lookup"><span data-stu-id="d9178-138">Start with a good disaster recovery plan, and test it periodically to make sure it works.</span></span>

<span data-ttu-id="d9178-139">Azure Virtual Machines については、[Azure Site Recovery](/azure/site-recovery/azure-to-azure-quickstart/) を使用することで、運用アプリケーションや進行中のレプリケーションに影響を与えることなく、レプリケーションおよび[ディザスター リカバリー訓練を実施](/azure/site-recovery/azure-to-azure-tutorial-dr-drill/)できます。</span><span class="sxs-lookup"><span data-stu-id="d9178-139">For Azure Virtual Machines, you can use [Azure Site Recovery](/azure/site-recovery/azure-to-azure-quickstart/) to replicate and perform [disaster recovery drills](/azure/site-recovery/azure-to-azure-tutorial-dr-drill/) without impacting production applications or ongoing replication.</span></span>

### <a name="failover-and-failback-testing"></a><span data-ttu-id="d9178-140">フェールオーバーとフェールバックのテスト</span><span class="sxs-lookup"><span data-stu-id="d9178-140">Failover and failback testing</span></span>

<span data-ttu-id="d9178-141">フェールオーバーとフェールバックをテストして、お使いのアプリケーションの依存サービスがディザスター リカバリー中に同期的にバックアップされることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d9178-141">Test failover and failback to verify that your application's dependent services come back up in a synchronized manner during disaster recovery.</span></span> <span data-ttu-id="d9178-142">システムおよび操作の変更はフェールオーバーおよびフォールバック機能に影響を与える可能性がありますが、その影響はメインのシステムで障害が発生するか、オーバーロードが発生するまで検出されない場合があります。</span><span class="sxs-lookup"><span data-stu-id="d9178-142">Changes to systems and operations may affect failover and failback functions, but the impact may not be detected until the main system fails or becomes overloaded.</span></span> <span data-ttu-id="d9178-143">フェールオーバー機能は、実際に起きている問題の補正で必要になる*前に*テストします。</span><span class="sxs-lookup"><span data-stu-id="d9178-143">Test failover capabilities *before* they are required to compensate for a live problem.</span></span> <span data-ttu-id="d9178-144">また、依存サービスのフェールオーバーとフェールバックが、確実に正しい順序で行われるようにしてください。</span><span class="sxs-lookup"><span data-stu-id="d9178-144">Also be sure that dependent services fail over and fail back in the correct order.</span></span>

<span data-ttu-id="d9178-145">[Azure Site Recovery](/azure/site-recovery/) を使用して VM をレプリケートする場合は、テスト フェールオーバーを実行してディザスター リカバリー訓練を定期的に実行し、レプリケーション戦略を検証します。</span><span class="sxs-lookup"><span data-stu-id="d9178-145">If you are using [Azure Site Recovery](/azure/site-recovery/) to replicate VMs, run disaster recovery drills periodically by doing test failovers to validate your replication strategy.</span></span> <span data-ttu-id="d9178-146">テスト フェールオーバーは、実行中の VM レプリケーションや運用環境に影響を与えません。</span><span class="sxs-lookup"><span data-stu-id="d9178-146">A test failover does not affect the ongoing VM replication or your production environment.</span></span> <span data-ttu-id="d9178-147">詳細については、「[Azure へのディザスター リカバリー訓練を実行する](/azure/site-recovery/site-recovery-test-failover-to-azure)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d9178-147">For more information, see [Run a disaster recovery drill to Azure](/azure/site-recovery/site-recovery-test-failover-to-azure).</span></span>

### <a name="simulation-testing"></a><span data-ttu-id="d9178-148">シミュレーション テスト</span><span class="sxs-lookup"><span data-stu-id="d9178-148">Simulation testing</span></span>

<span data-ttu-id="d9178-149">シミュレーション テストには、小規模の実際の状況の作成が必要です。</span><span class="sxs-lookup"><span data-stu-id="d9178-149">Simulation testing involves creating small, real-life situations.</span></span> <span data-ttu-id="d9178-150">シミュレーションでは、復旧計画のソリューションの有効性を実証し、適切に対処されないすべての問題を明らかにします。</span><span class="sxs-lookup"><span data-stu-id="d9178-150">Simulations demonstrate the effectiveness of the solutions in the recovery plan and highlight any issues that weren't adequately addressed.</span></span>

<span data-ttu-id="d9178-151">シミュレーション テストを実行する際は、次のベスト プラクティスに従います。</span><span class="sxs-lookup"><span data-stu-id="d9178-151">As you perform simulation testing, follow best practices:</span></span>

- <span data-ttu-id="d9178-152">実際の業務は中断しないが、実際の状況に近い方法でシミュレーションを実施します。</span><span class="sxs-lookup"><span data-stu-id="d9178-152">Conduct simulations in a manner that doesn't disrupt actual business but feels like a real situation.</span></span>
- <span data-ttu-id="d9178-153">シミュレートされるシナリオは、完全に制御できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="d9178-153">Make sure that simulated scenarios are completely controllable.</span></span> <span data-ttu-id="d9178-154">復旧計画が失敗したと思われる場合、損害を発生させずに状況を正常な状態に戻せる必要があります。</span><span class="sxs-lookup"><span data-stu-id="d9178-154">If the recovery plan seems to be failing, you can restore the situation back to normal without causing damage.</span></span>
- <span data-ttu-id="d9178-155">シミュレーションを実施する時期と方法について管理者に通知します。</span><span class="sxs-lookup"><span data-stu-id="d9178-155">Inform management about when and how the simulation exercises will be conducted.</span></span> <span data-ttu-id="d9178-156">計画では、シミュレーション中に影響を受ける時間帯とリソースを具体的に示す必要があります。</span><span class="sxs-lookup"><span data-stu-id="d9178-156">Your plan should detail the time frame and the resources affected during the simulation.</span></span>

## <a name="test-health-probes-for-load-balancers-and-traffic-managers"></a><span data-ttu-id="d9178-157">ロード バランサーとトラフィック マネージャーの正常性プローブをテストする</span><span class="sxs-lookup"><span data-stu-id="d9178-157">Test health probes for load balancers and traffic managers</span></span>

<span data-ttu-id="d9178-158">ロード バランサーとトラフィック マネージャーの正常性プローブを構成し、テストします。</span><span class="sxs-lookup"><span data-stu-id="d9178-158">Configure and test health probes for your load balancers and traffic managers.</span></span> <span data-ttu-id="d9178-159">正常性エンドポイントによりシステムの重要な部分が確実にチェックされ、適切に応答するようにします。</span><span class="sxs-lookup"><span data-stu-id="d9178-159">Ensure that your health endpoint checks the critical parts of the system and responds appropriately.</span></span>

- <span data-ttu-id="d9178-160">[Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview/) の場合、正常性プローブは別のリージョンにフェールオーバーするかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="d9178-160">For [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview/), the health probe determines whether to fail over to another region.</span></span> <span data-ttu-id="d9178-161">正常性エンドポイントは、同じリージョン内にデプロイされるすべてのクリティカルな依存関係を確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d9178-161">Your health endpoint should check any critical dependencies that are deployed within the same region.</span></span>
- <span data-ttu-id="d9178-162">[Azure Load Balancer](/azure/load-balancer/load-balancer-overview/) の場合、VM をローテーションから除去するかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="d9178-162">For [Azure Load Balancer](/azure/load-balancer/load-balancer-overview/), the health probe determines whether to remove a VM from rotation.</span></span> <span data-ttu-id="d9178-163">正常性エンドポイントが VM の正常性をレポートします。</span><span class="sxs-lookup"><span data-stu-id="d9178-163">The health endpoint should report the health of the VM.</span></span> <span data-ttu-id="d9178-164">その他の階層または外部サービスを含めないでください。</span><span class="sxs-lookup"><span data-stu-id="d9178-164">Don't include other tiers or external services.</span></span> <span data-ttu-id="d9178-165">そうしないと、VM の外部で発生する障害によってロード バランサーがローテーションから VM を除去することになります。</span><span class="sxs-lookup"><span data-stu-id="d9178-165">Otherwise, a failure that occurs outside the VM will cause the load balancer to remove the VM from rotation.</span></span>

<span data-ttu-id="d9178-166">アプリケーションに正常性の監視を実装する方法については、「[正常性エンドポイント監視パターン](../patterns/health-endpoint-monitoring.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d9178-166">For guidance on implementing health monitoring in your application, see [Health Endpoint Monitoring pattern](../patterns/health-endpoint-monitoring.md).</span></span>

## <a name="test-monitoring-systems"></a><span data-ttu-id="d9178-167">監視システムをテストする</span><span class="sxs-lookup"><span data-stu-id="d9178-167">Test monitoring systems</span></span>

<span data-ttu-id="d9178-168">テスト計画に監視システムを含めます。</span><span class="sxs-lookup"><span data-stu-id="d9178-168">Include monitoring systems in your test plan.</span></span> <span data-ttu-id="d9178-169">自動フェールオーバーとフェールバックのシステムは、監視とインストルメンテーションが正しく動作するかどうかに依存します。</span><span class="sxs-lookup"><span data-stu-id="d9178-169">Automated failover and failback systems depend on the correct functioning of monitoring and instrumentation.</span></span> <span data-ttu-id="d9178-170">システムの正常性と演算子のアラートを視覚化するダッシュボードも、正確な監視とインストルメンテーションがあるかどうかに依存します。</span><span class="sxs-lookup"><span data-stu-id="d9178-170">Dashboards to visualize system health and operator alerts also depend on having accurate monitoring and instrumentation.</span></span> <span data-ttu-id="d9178-171">これらの要素で障害、重要な情報の不足、不適切なデータの報告が発生すると、オペレーターはシステムが正常でないことやシステムで障害が発生していることに気付かない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d9178-171">If these elements fail, miss critical information, or report inaccurate data, an operator might not realize that the system is unhealthy or failing.</span></span>

## <a name="include-third-party-services-in-test-scenarios"></a><span data-ttu-id="d9178-172">テスト シナリオにサード パーティーのサービスを含める</span><span class="sxs-lookup"><span data-stu-id="d9178-172">Include third-party services in test scenarios</span></span>

<span data-ttu-id="d9178-173">アプリケーションがサード パーティーのサービスに依存している場合は、それらのサード パーティーのサービスがどこでどのように失敗する可能性があるのかと、それらの障害がアプリケーションにどのような影響を与えるかを特定してください。</span><span class="sxs-lookup"><span data-stu-id="d9178-173">If your application has dependencies on third-party services, identify where and how these services can fail and what effect those failures will have on your application.</span></span> <span data-ttu-id="d9178-174">サード パーティーのサービスのサービス レベル アグリーメント (SLA) とそれがディザスター リカバリー計画に及ぼす影響を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="d9178-174">Keep in mind the service-level agreement (SLA) for the third-party service and the effect it might have on your disaster recovery plan.</span></span>

<span data-ttu-id="d9178-175">サード パーティーのサービスでは監視と診断の機能が提供されないことがあるため、それらの呼び出しをログに記録し、一意の識別子を使用してアプリケーションの正常性と診断のログと関連付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="d9178-175">A third-party service might not provide monitoring and diagnostics capabilities, so it's important to log your invocations of them and to correlate them with your application's health and diagnostic logging using a unique identifier.</span></span> <span data-ttu-id="d9178-176">監視と診断の実証された方法については、[監視と診断のガイダンス](../best-practices/monitoring.md)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d9178-176">For more information on proven practices for monitoring and diagnostics, see [Monitoring and diagnostics guidance](../best-practices/monitoring.md).</span></span>

## <a name="next-steps"></a><span data-ttu-id="d9178-177">次の手順</span><span class="sxs-lookup"><span data-stu-id="d9178-177">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="d9178-178">回復性と可用性のためのデプロイ</span><span class="sxs-lookup"><span data-stu-id="d9178-178">Deploy for resiliency and availability</span></span>](./deploy.md)
