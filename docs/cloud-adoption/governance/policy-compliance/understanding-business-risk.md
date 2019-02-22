---
title: CAF:ビジネス リスクはクラウドでどのように変わりますか?
description: 移行時のビジネス リスクの理解
author: BrianBlanchard
ms.date: 10/10/2018
ms.openlocfilehash: 458474f3c94c5df4f7ffef439095adf138f33d78
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55902142"
---
<!-- markdownlint-disable MD026 -->

# <a name="how-does-business-risk-change-in-the-cloud"></a><span data-ttu-id="39ef3-103">ビジネス リスクはクラウドでどのように変わりますか?</span><span class="sxs-lookup"><span data-stu-id="39ef3-103">How does business risk change in the cloud?</span></span>

<span data-ttu-id="39ef3-104">ビジネス リスクを理解することは、あらゆるクラウド変換において最も重要な要素の 1 つです。</span><span class="sxs-lookup"><span data-stu-id="39ef3-104">An understanding of business risk is one of the most important elements of any cloud transformation.</span></span> <span data-ttu-id="39ef3-105">リスクによってポリシーは左右され、監視と適用の要件に影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-105">Risk drives policy, it influences monitoring and enforcement requirements.</span></span> <span data-ttu-id="39ef3-106">リスクは、会社がオンプレミスまたはクラウドでデジタル資産を管理する方法に大きく影響します。</span><span class="sxs-lookup"><span data-stu-id="39ef3-106">Risk heavily influences how we manage the digital estate, on-premises or in the cloud.</span></span>

<!-- markdownlint-enable MD026 -->

## <a name="relativity-of-risk"></a><span data-ttu-id="39ef3-107">リスクの相対性</span><span class="sxs-lookup"><span data-stu-id="39ef3-107">Relativity of risk</span></span>

<span data-ttu-id="39ef3-108">リスクは相対的です。</span><span class="sxs-lookup"><span data-stu-id="39ef3-108">Risk is relative.</span></span> <span data-ttu-id="39ef3-109">閉ざされた建物の中に少数の IT 資産を持っている小規模な会社は、ほとんどリスクがありません。</span><span class="sxs-lookup"><span data-stu-id="39ef3-109">A small company with a few IT assets, in a closed building has little risk.</span></span> <span data-ttu-id="39ef3-110">このような資産へのアクセス権を持つユーザーとインターネット接続を追加すると、リスクは高まります。</span><span class="sxs-lookup"><span data-stu-id="39ef3-110">Add users and an internet connection with access to those assets, the risk is intensified.</span></span> <span data-ttu-id="39ef3-111">その小規模な企業が Fortune 500 を獲得するまでに成長すると、リスクは急激に大きくなります。</span><span class="sxs-lookup"><span data-stu-id="39ef3-111">When that small company grows to Fortune 500 status, the risks are exponentially greater.</span></span> <span data-ttu-id="39ef3-112">収益、ビジネス プロセス、従業員数、IT 資産が蓄積するにつれて、リスクは高まり、結合します。</span><span class="sxs-lookup"><span data-stu-id="39ef3-112">As revenue, business process, employee counts, and IT assets accumulate, risks increase and coalesce.</span></span> <span data-ttu-id="39ef3-113">収益を生み出すために役立つ IT 資産には、停止した場合にその収益の流れが止まるという明白なリスクがあります。</span><span class="sxs-lookup"><span data-stu-id="39ef3-113">IT assets that aid in generating revenue are at tangible risk of stopping that revenue stream in the event of an outage.</span></span> <span data-ttu-id="39ef3-114">ダウンタイムのあらゆる瞬間が損失に相当します。</span><span class="sxs-lookup"><span data-stu-id="39ef3-114">Every moment of downtime equates to losses.</span></span> <span data-ttu-id="39ef3-115">同様に、データが蓄積するにつれて、顧客に害を及ぼすリスクが高まります。</span><span class="sxs-lookup"><span data-stu-id="39ef3-115">Likewise, as data accumulates, the risk of harming customers grows.</span></span>

<span data-ttu-id="39ef3-116">従来のオンプレミスの世界では、IT ガバナンス チームはこのようなリスクの評価を重視します。</span><span class="sxs-lookup"><span data-stu-id="39ef3-116">In the traditional on-premises world, IT governance teams focus on assessing those risks.</span></span> <span data-ttu-id="39ef3-117">リスクを軽減するためのプロセスを作成します。</span><span class="sxs-lookup"><span data-stu-id="39ef3-117">Creating processes to mitigate the risk.</span></span> <span data-ttu-id="39ef3-118">確実に軽減策が成功し、実装できるシステムをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="39ef3-118">Deploying systems to ensure mitigation measures are successful and implemented.</span></span> <span data-ttu-id="39ef3-119">このようにして、接続された現代のビジネス環境での運用に必要なリスクとバランスを取ります。</span><span class="sxs-lookup"><span data-stu-id="39ef3-119">This balances the risks required to operate in a connected, modern business environment.</span></span>

## <a name="understanding-business-risks-in-the-cloud"></a><span data-ttu-id="39ef3-120">クラウドでのビジネス リスクの理解</span><span class="sxs-lookup"><span data-stu-id="39ef3-120">Understanding business risks in the cloud</span></span>

<span data-ttu-id="39ef3-121">変換時は、同じ相対リスクが見られます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-121">During a transformation, the same relative risks can be seen.</span></span>

* <span data-ttu-id="39ef3-122">初期の実験段階では、関連データがほとんどないか、まったくない少数の資産がデプロイされます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-122">During early experimentation, a few assets are deployed with little to no relevant data.</span></span> <span data-ttu-id="39ef3-123">リスクは小規模です。</span><span class="sxs-lookup"><span data-stu-id="39ef3-123">The risk is small.</span></span>
* <span data-ttu-id="39ef3-124">最初のワークロードがデプロイされると、リスクは少し上がります。</span><span class="sxs-lookup"><span data-stu-id="39ef3-124">When the first workload is deployed, risk goes up a little.</span></span> <span data-ttu-id="39ef3-125">このリスクは、ユーザー ベースが小規模な本質的に低リスクのアプリケーションを選択することで簡単に軽減できます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-125">This risk is easily mitigated by choosing an inherently low risk application with a small user base.</span></span>
* <span data-ttu-id="39ef3-126">より多くのワークロードがオンラインになるにつれて、リスクはリリースごとに変わります。</span><span class="sxs-lookup"><span data-stu-id="39ef3-126">As more workloads come online, risks change at each release.</span></span> <span data-ttu-id="39ef3-127">新しいアプリの利用が開始されると、リスクが変わります。</span><span class="sxs-lookup"><span data-stu-id="39ef3-127">New apps go live, risks change.</span></span>
* <span data-ttu-id="39ef3-128">ある会社が最初の 10-20 個のアプリケーションをオンラインに移行したときと、1,000 個目のアプリケーションをクラウドでの運用に移行したときでは、リスク プロファイルは大きく異なります。</span><span class="sxs-lookup"><span data-stu-id="39ef3-128">When a company brings the first 10-20 applications online, the risk profile is much different that it is when the 1000th applications go into production in the cloud.</span></span>

<span data-ttu-id="39ef3-129">従来のオンプレミスの地所に蓄積された資産は、おそらく徐々に蓄積されたものです。</span><span class="sxs-lookup"><span data-stu-id="39ef3-129">The assets that accumulated in the traditional, on-premises estate likely accumulated overtime.</span></span> <span data-ttu-id="39ef3-130">ビジネス チームと IT チームの成熟度は、おそらく同じように成長していました。</span><span class="sxs-lookup"><span data-stu-id="39ef3-130">The maturity of the business and IT teams was likely growing in a similar fashion.</span></span> <span data-ttu-id="39ef3-131">このような並行した成長によって、不必要なポリシーの荷物が生じる傾向があります。</span><span class="sxs-lookup"><span data-stu-id="39ef3-131">That parallel growth can tend to create some unnecessary policy baggage.</span></span>

<span data-ttu-id="39ef3-132">クラウド変換時は、ビジネス チームと IT チームの両方が、成熟した考え方でこのようなポリシーを再設定し、新たに構築する機会を得ます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-132">During a cloud transformation, both the business and IT teams have an opportunity to reset those policies and build new with a matured mindset.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="what-is-a-business-risk-mvp"></a><span data-ttu-id="39ef3-133">ビジネス リスク MVP とは</span><span class="sxs-lookup"><span data-stu-id="39ef3-133">What is a business risk MVP?</span></span>

<span data-ttu-id="39ef3-134">**実用最小限の製品**とは、具体的な価値を生み出すことができる何かの最小限の単位を定義するための業界標準の用語です。</span><span class="sxs-lookup"><span data-stu-id="39ef3-134">**Minimum viable product** is an industry-standard term for defining the smallest unit of something that can produce tangible value.</span></span> <span data-ttu-id="39ef3-135">ビジネス リスク MVP では、チームは、一部の資産がクラウド環境にデプロイされるという前提条件から始めます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-135">In a business risk MVP, the team starts with an assumption that some assets will be deployed to a cloud environment.</span></span> <span data-ttu-id="39ef3-136">それらの資産が何であるかは、この時点では不明です。</span><span class="sxs-lookup"><span data-stu-id="39ef3-136">It's unknown at the time what those assets are.</span></span> <span data-ttu-id="39ef3-137">これらの資産で処理されるデータの種類も不明です。</span><span class="sxs-lookup"><span data-stu-id="39ef3-137">It's also unknown what types of data will be processed by those assets.</span></span>

<span data-ttu-id="39ef3-138">クラウド ガバナンス チームは、最悪のシナリオを想定して構築し、考えられるすべてのポリシーをクラウドにマッピングすることができます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-138">The Cloud Governance team could build for the worst-case scenario and map every possible policy to the cloud.</span></span> <span data-ttu-id="39ef3-139">これはお勧めできませんが、1 つの選択肢です。</span><span class="sxs-lookup"><span data-stu-id="39ef3-139">This is not advised, but is an option.</span></span>

<span data-ttu-id="39ef3-140">逆に、チームは MVP アプローチを採用し、大部分またはすべての資産に該当する開始点と一連の前提条件を定義することができます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-140">Conversely, the team could take an MVP approach and define a starting point and set of assumptions that would be true for most/all assets.</span></span>
<span data-ttu-id="39ef3-141">非常に基本的な例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="39ef3-141">The following are a few extremely basic examples:</span></span>

* <span data-ttu-id="39ef3-142">すべての資産は、(エラー、誤り、またはメンテナンスによって) 停止する危険があります</span><span class="sxs-lookup"><span data-stu-id="39ef3-142">All assets are at risk of being terminated (through error, mistake or maintenance)</span></span>
* <span data-ttu-id="39ef3-143">すべての資産には、支出が高くなりすぎるリスクがあります</span><span class="sxs-lookup"><span data-stu-id="39ef3-143">All assets are at risk of generating too much spending</span></span>
* <span data-ttu-id="39ef3-144">すべての資産は、脆弱なパスワードによって侵害される可能性があります</span><span class="sxs-lookup"><span data-stu-id="39ef3-144">All assets could be compromised by weak passwords</span></span>
* <span data-ttu-id="39ef3-145">インターネットに公開されたあらゆるオープン ポートを持つすべての資産は、侵害されるリスクがあります</span><span class="sxs-lookup"><span data-stu-id="39ef3-145">Any asset with all open ports exposed to the internet are at risk of compromise</span></span>

<span data-ttu-id="39ef3-146">上記の例は、理論として MVP ビジネス リスクを確立するためのものです。</span><span class="sxs-lookup"><span data-stu-id="39ef3-146">The above examples are meant to establish MVP business risks as a theory.</span></span> <span data-ttu-id="39ef3-147">実際の一覧は各環境に固有のものになります。</span><span class="sxs-lookup"><span data-stu-id="39ef3-147">The actual list will be unique to every environment.</span></span>
<span data-ttu-id="39ef3-148">ビジネス リスク MVP を確立すると、それらを[ポリシー](overview.md)に変換して各リスクを軽減できます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-148">Once the Business Risk MVP is established, they can be converted to [Policies](overview.md) to mitigate each risk.</span></span>

<!-- markdownlint-enable MD026 -->

## <a name="incremental-risk-mitigation"></a><span data-ttu-id="39ef3-149">増分リスク軽減策</span><span class="sxs-lookup"><span data-stu-id="39ef3-149">Incremental risk mitigation</span></span>

<span data-ttu-id="39ef3-150">ビジネス リスク MVP が出発点であると仮定すると、(ビジネスの成長と並行して成長するのではなく) 計画されたデプロイと並行してガバナンスを成熟させることができます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-150">Assuming a business risk MVP is the starting point, governance can mature in parallel to planned deployment (as opposed to growing in parallel to business growth).</span></span> <span data-ttu-id="39ef3-151">これは、はるかに安定したガバナンスの成熟度のモデルです。</span><span class="sxs-lookup"><span data-stu-id="39ef3-151">This is a much more stable model for governance maturity.</span></span> <span data-ttu-id="39ef3-152">繰り返しごとに、新しい資産がレプリケートされ、ステージングされます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-152">At each iteration, new assets are replicated and staged.</span></span> <span data-ttu-id="39ef3-153">リリースごとに、運用促進のためのワークロードが用意されます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-153">At each release, workloads are readied for production promotion.</span></span> <span data-ttu-id="39ef3-154">当然ながら、相対リスクはサイクルごとに大きくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="39ef3-154">Of course, the relative risk could grow with each cycle.</span></span>

<span data-ttu-id="39ef3-155">クラウド ガバナンス チームがクラウド導入チームと並行して活動している場合は、ビジネス リスクの増大にも同様に対処できます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-155">When the Cloud Governance team operates in parallel to the cloud adoption teams, the growth of business risks can likewise be addressed.</span></span> <span data-ttu-id="39ef3-156">ステージングされた各資産は、リスクに従って容易に分類することができます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-156">Each asset staged can easily be classified according to risk.</span></span> <span data-ttu-id="39ef3-157">データ分類ドキュメントは、ステージングと並行して構築または作成できます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-157">Data classification documents can be built or created in parallel to staging.</span></span> <span data-ttu-id="39ef3-158">リスク プロファイルと曝露点も同様にドキュメント化することができます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-158">Risk profile and exposure points can likewise be documented.</span></span> <span data-ttu-id="39ef3-159">組織全体で、ビジネス リスクに対する非常に明確なビジョンが徐々に重視されるようになります。</span><span class="sxs-lookup"><span data-stu-id="39ef3-159">Overtime an extremely clear view of business risk wil come into focus across the organization.</span></span>

<span data-ttu-id="39ef3-160">繰り返しごとに、クラウド ガバナンス チームはクラウド戦略チームと連携し、新しいリスク、軽減戦略、トレードオフ、発生する可能性があるコストについてすぐに伝えることができます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-160">With each iteration, the Cloud Governance team can work with Cloud Strategy team to quickly communicate new risks, mitigation strategies, tradeoffs, and potential costs.</span></span> <span data-ttu-id="39ef3-161">その結果、業務の関係者と IT リーダーは、成熟した、十分な情報に基づいた決定に参加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="39ef3-161">This empowers business participants and IT leaders to partner in mature, well-informed decisions.</span></span> <span data-ttu-id="39ef3-162">このような決定によって、ポリシーの成熟度がわかります。</span><span class="sxs-lookup"><span data-stu-id="39ef3-162">Those decisions then inform policy maturity.</span></span> <span data-ttu-id="39ef3-163">必要に応じて、ポリシーの変更により、コア インフラストラクチャ システムの成熟度に合わせて新しい作業項目が作成されます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-163">When required, the policy changes produce new work items for the maturity of core infrastructure systems.</span></span> <span data-ttu-id="39ef3-164">ステージングされたシステムの変更が必要な場合、クラウド導入チームには変更を実行できる十分な時間がありますが、ビジネス チームがステージングされたシステムをテストし、ユーザー導入計画を立てます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-164">When changes to staged systems are required, the cloud adoption teams have ample time to make changes, while the business tests the staged systems and develops a user adoption plan.</span></span>

<span data-ttu-id="39ef3-165">このアプローチでリスクを最小限に抑えながら、チームが迅速に動けるようにします。</span><span class="sxs-lookup"><span data-stu-id="39ef3-165">This approach minimizes risks, while empowering the team to move quickly.</span></span> <span data-ttu-id="39ef3-166">また、デプロイ前にリスクを迅速に識別して解決することもできます。</span><span class="sxs-lookup"><span data-stu-id="39ef3-166">It also ensures that risks are promptly identified and resolved before deployment.</span></span>

## <a name="next-steps"></a><span data-ttu-id="39ef3-167">次の手順</span><span class="sxs-lookup"><span data-stu-id="39ef3-167">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="39ef3-168">リスク許容度の評価</span><span class="sxs-lookup"><span data-stu-id="39ef3-168">Evaluate risk tolerance</span></span>](./risk-tolerance.md)