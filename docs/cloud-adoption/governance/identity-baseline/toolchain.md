---
title: CAF:Azure での ID ベースライン ツール
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Azure での ID ベースライン ツール
author: BrianBlanchard
ms.openlocfilehash: 81b0fa9cfee597da98d8b983fb155eac82d97bf8
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901921"
---
# <a name="identity-baseline-tools-in-azure"></a><span data-ttu-id="9c0ed-103">Azure での ID ベースライン ツール</span><span class="sxs-lookup"><span data-stu-id="9c0ed-103">Identity Baseline tools in Azure</span></span>

<span data-ttu-id="9c0ed-104">[ID ベースライン](overview.md)は、[5 つのクラウド ガバナンス規範](../governance-disciplines.md)のうちの 1 つです。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-104">[Identity Baseline](overview.md) is one of the [Five Disciplines of Cloud Governance](../governance-disciplines.md).</span></span> <span data-ttu-id="9c0ed-105">この規範では、アプリケーションまたはワークロードをホストするクラウド プロバイダーに関係なく、ユーザー ID の一貫性と継続性を保証するポリシーを確立する方法に焦点を当てています。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-105">This discipline focuses on ways of establishing policies that ensure consistency and continuity of user identities regardless of the cloud provider that hosts the application or workload.</span></span>

<span data-ttu-id="9c0ed-106">ハイブリッド ID の検出ガイドには、次のツールが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-106">The following tools are included in the discovery guide on Hybrid Identity.</span></span>

<span data-ttu-id="9c0ed-107">**Active Directory (オンプレミス):** Active Directory は、ユーザーの資格情報を保存および検証するために企業で使用されることが最も多い ID プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-107">**Active Directory (on-premises):** Active Directory is the identity provider most frequently used in the enterprise to store and validate user credentials.</span></span>

<span data-ttu-id="9c0ed-108">**Azure Active Directory:** サービスとしてのソフトウェア (SaaS)。Active Directory と同等の機能を持ち、オンプレミスの Active Directory とのフェデレーションが可能です。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-108">**Azure Active Directory:** A software as a service (SaaS) equivalent to Active Directory, capable of federating with an on-premises Active Directory.</span></span>

<span data-ttu-id="9c0ed-109">**Active Directory (IaaS):** Azure の仮想マシンで実行されている Active Directory アプリケーションのインスタンス。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-109">**Active Directory (IaaS):** An instance of the Active Directory application running in a virtual machine in Azure.</span></span>

<span data-ttu-id="9c0ed-110">ID は IT セキュリティのコントロール プレーンです。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-110">Identity is the control plane for IT security.</span></span> <span data-ttu-id="9c0ed-111">したがって認証は、クラウドに対する組織のアクセスの保護です。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-111">So authentication is an organization’s access guard to the cloud.</span></span> <span data-ttu-id="9c0ed-112">組織には、セキュリティを強化し、クラウド アプリを侵入者から保護する ID コントロール プレーンが必要です。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-112">Organizations need an identity control plane that strengthens their security and keeps their cloud apps safe from intruders.</span></span>

## <a name="cloud-authentication"></a><span data-ttu-id="9c0ed-113">クラウド認証</span><span class="sxs-lookup"><span data-stu-id="9c0ed-113">Cloud authentication</span></span>

<span data-ttu-id="9c0ed-114">正しい認証方法の選択は、クラウドにアプリを移行しようとしている組織にとって最大の関心事です。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-114">Choosing the correct authentication method is the first concern for organizations wanting to move their apps to the cloud.</span></span>

<span data-ttu-id="9c0ed-115">この方法を選ぶと、Azure AD がユーザーのサインイン プロセスを処理します。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-115">When you choose this method, Azure AD handles users' sign-in process.</span></span> <span data-ttu-id="9c0ed-116">シームレスなシングル サインオン (SSO) と組み合わせることで、ユーザーは資格情報を再入力しなくてもクラウド アプリにサインインできます。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-116">Coupled with seamless single sign-on (SSO), users can sign in to cloud apps without having to reenter their credentials.</span></span> <span data-ttu-id="9c0ed-117">クラウド認証では、2 つのオプションから選ぶことができます。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-117">With cloud authentication, you can choose from two options:</span></span>

<span data-ttu-id="9c0ed-118">**Azure AD のパスワード ハッシュ同期**:Azure AD でオンプレミスのディレクトリ オブジェクトの認証を有効にする最も簡単な方法です。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-118">**Azure AD password hash synchronization**: The simplest way to enable authentication for on-premises directory objects in Azure AD.</span></span> <span data-ttu-id="9c0ed-119">この方法は、オンプレミスのサーバーがダウンした場合のバックアップ フェールオーバー認証方法として、任意の方法と一緒に使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-119">This method can also be used with any method as a back-up failover authentication method in case your on-premises server goes down.</span></span>

<span data-ttu-id="9c0ed-120">**Azure AD パススルー認証**:1 つ以上のオンプレミス サーバーで実行されているソフトウェア エージェントを使用して、Azure AD 認証サービスに永続的なパスワード検証を提供します。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-120">**Azure AD Pass-through Authentication**: Provides a persistent password validation for Azure AD authentication services by using a software agent that runs on one or more on-premises servers.</span></span>

> [!NOTE]
> <span data-ttu-id="9c0ed-121">オンプレミスのユーザー アカウントの状態、パスワード ポリシー、およびサインイン時間をすぐに適用するセキュリティ要件のある企業では、パススルー認証方法を検討してください。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-121">Companies with a security requirement to immediately enforce on-premises user account states, password policies, and sign-in hours should consider the pass-through Authentication method.</span></span>

<span data-ttu-id="9c0ed-122">**フェデレーション認証:**</span><span class="sxs-lookup"><span data-stu-id="9c0ed-122">**Federated authentication:**</span></span>

<span data-ttu-id="9c0ed-123">この方法を選ぶと、Azure AD は別の信頼された認証システム (オンプレミスの Active Directory フェデレーション サービス (AD FS) や、信頼できるサード パーティのフェデレーション プロバイダーなど) に、ユーザーのパスワードを検証する認証プロセスを引き渡します。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-123">When you choose this method, Azure AD passes the authentication process to a separate trusted authentication system, such as on-premises Active Directory Federation Services (AD FS) or a trusted third-party federation provider, to validate the user’s password.</span></span>

<span data-ttu-id="9c0ed-124">[Azure Active Directory 用の適切な認証方法の選択](/azure/security/azure-ad-choose-authn)に関する記事には、組織に最適なソリューションを選択するためのデシジョン ツリーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-124">The article [choosing the right authentication method for Azure Active Directory](/azure/security/azure-ad-choose-authn) contains a decision tree to help you choose the best solution for your organization.</span></span>

<span data-ttu-id="9c0ed-125">次の表は、このガバナンス規範をサポートするポリシーとプロセスを成熟させるのに役立つネイティブ ツールの一覧です。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-125">The following table lists the native tools that can help mature the policies and processes that support this governance discipline.</span></span>

<!-- markdownlint-disable MD033 -->

|<span data-ttu-id="9c0ed-126">考慮事項</span><span class="sxs-lookup"><span data-stu-id="9c0ed-126">Consideration</span></span>|<span data-ttu-id="9c0ed-127">パスワード ハッシュ同期 + シームレス SSO</span><span class="sxs-lookup"><span data-stu-id="9c0ed-127">Password hash synchronization + Seamless SSO</span></span>|<span data-ttu-id="9c0ed-128">パススルー認証 + シームレス SSO</span><span class="sxs-lookup"><span data-stu-id="9c0ed-128">Pass-through Authentication + Seamless SSO</span></span>|<span data-ttu-id="9c0ed-129">AD FS とのフェデレーション</span><span class="sxs-lookup"><span data-stu-id="9c0ed-129">Federation with AD FS</span></span>|
|:-----|:-----|:-----|:-----|
|<span data-ttu-id="9c0ed-130">認証が行われる場所</span><span class="sxs-lookup"><span data-stu-id="9c0ed-130">Where does authentication happen?</span></span>|<span data-ttu-id="9c0ed-131">クラウド内</span><span class="sxs-lookup"><span data-stu-id="9c0ed-131">In the cloud</span></span>|<span data-ttu-id="9c0ed-132">クラウド内で、オンプレミスの認証エージェントとのセキュリティで保護されたパスワード検証の交換後</span><span class="sxs-lookup"><span data-stu-id="9c0ed-132">In the cloud after a secure password verification exchange with the on-premises authentication agent</span></span>|<span data-ttu-id="9c0ed-133">オンプレミス</span><span class="sxs-lookup"><span data-stu-id="9c0ed-133">On-premises</span></span>|
|<span data-ttu-id="9c0ed-134">プロビジョニング システム以外のオンプレミスのサーバーの要件: Azure AD Connect</span><span class="sxs-lookup"><span data-stu-id="9c0ed-134">What are the on-premises server requirements beyond the provisioning system: Azure AD Connect?</span></span>|<span data-ttu-id="9c0ed-135">なし</span><span class="sxs-lookup"><span data-stu-id="9c0ed-135">None</span></span>|<span data-ttu-id="9c0ed-136">追加の認証エージェントごとに 1 つのサーバー</span><span class="sxs-lookup"><span data-stu-id="9c0ed-136">One server for each additional authentication agent</span></span>|<span data-ttu-id="9c0ed-137">2 つ以上の AD FS サーバー</span><span class="sxs-lookup"><span data-stu-id="9c0ed-137">Two or more AD FS servers</span></span><br><br><span data-ttu-id="9c0ed-138">境界/DMZ ネットワークに 2 つ以上の WAP サーバー</span><span class="sxs-lookup"><span data-stu-id="9c0ed-138">Two or more WAP servers in the perimeter/DMZ network</span></span>|
|<span data-ttu-id="9c0ed-139">プロビジョニング システム以外のオンプレミスのインターネットおよびネットワークの要件</span><span class="sxs-lookup"><span data-stu-id="9c0ed-139">What are the requirements for on-premises Internet and networking beyond the provisioning system?</span></span>|<span data-ttu-id="9c0ed-140">なし</span><span class="sxs-lookup"><span data-stu-id="9c0ed-140">None</span></span>|<span data-ttu-id="9c0ed-141">認証エージェントを実行しているサーバーからの[発信インターネット アクセス](/azure/active-directory/hybrid/how-to-connect-pta-quick-start)</span><span class="sxs-lookup"><span data-stu-id="9c0ed-141">[Outbound Internet access](/azure/active-directory/hybrid/how-to-connect-pta-quick-start) from the servers running authentication agents</span></span>|<span data-ttu-id="9c0ed-142">境界の WAP サーバーへの[着信インターネット アクセス](/windows-server/identity/ad-fs/overview/ad-fs-requirements)</span><span class="sxs-lookup"><span data-stu-id="9c0ed-142">[Inbound Internet access](/windows-server/identity/ad-fs/overview/ad-fs-requirements) to WAP servers in the perimeter</span></span><br><br><span data-ttu-id="9c0ed-143">境界の WAP サーバーから AD FS サーバーへの着信ネットワーク アクセス</span><span class="sxs-lookup"><span data-stu-id="9c0ed-143">Inbound network access to AD FS servers from WAP servers in the perimeter</span></span><br><br><span data-ttu-id="9c0ed-144">ネットワークの負荷分散</span><span class="sxs-lookup"><span data-stu-id="9c0ed-144">Network load balancing</span></span>|
|<span data-ttu-id="9c0ed-145">SSL 証明書の要件</span><span class="sxs-lookup"><span data-stu-id="9c0ed-145">Is there an SSL certificate requirement?</span></span>|<span data-ttu-id="9c0ed-146">いいえ </span><span class="sxs-lookup"><span data-stu-id="9c0ed-146">No</span></span>|<span data-ttu-id="9c0ed-147">いいえ </span><span class="sxs-lookup"><span data-stu-id="9c0ed-147">No</span></span>|<span data-ttu-id="9c0ed-148">はい</span><span class="sxs-lookup"><span data-stu-id="9c0ed-148">Yes</span></span>|
|<span data-ttu-id="9c0ed-149">正常性の監視ソリューション</span><span class="sxs-lookup"><span data-stu-id="9c0ed-149">Is there a health monitoring solution?</span></span>|<span data-ttu-id="9c0ed-150">必要なし</span><span class="sxs-lookup"><span data-stu-id="9c0ed-150">Not required</span></span>|<span data-ttu-id="9c0ed-151">エージェントの状態は [Azure Active Directory 管理センター](/azure/active-directory/hybrid/tshoot-connect-pass-through-authentication)によって提供される</span><span class="sxs-lookup"><span data-stu-id="9c0ed-151">Agent status provided by [Azure Active Directory admin center](/azure/active-directory/hybrid/tshoot-connect-pass-through-authentication)</span></span>|[<span data-ttu-id="9c0ed-152">Azure AD Connect Health</span><span class="sxs-lookup"><span data-stu-id="9c0ed-152">Azure AD Connect Health</span></span>](/azure/active-directory/hybrid/how-to-connect-health-adfs)|
|<span data-ttu-id="9c0ed-153">会社のネットワーク内のドメインに参加しているデバイスからクラウドのリソースへのユーザーのシングル サインオン</span><span class="sxs-lookup"><span data-stu-id="9c0ed-153">Do users get single sign-on to cloud resources from domain-joined devices within the company network?</span></span>|<span data-ttu-id="9c0ed-154">[シームレス SSO](/azure/active-directory/hybrid/how-to-connect-sso) を使用して実行</span><span class="sxs-lookup"><span data-stu-id="9c0ed-154">Yes with [Seamless SSO](/azure/active-directory/hybrid/how-to-connect-sso)</span></span>|<span data-ttu-id="9c0ed-155">[シームレス SSO](/azure/active-directory/hybrid/how-to-connect-sso) を使用して実行</span><span class="sxs-lookup"><span data-stu-id="9c0ed-155">Yes with [Seamless SSO](/azure/active-directory/hybrid/how-to-connect-sso)</span></span>|<span data-ttu-id="9c0ed-156">はい</span><span class="sxs-lookup"><span data-stu-id="9c0ed-156">Yes</span></span>|
|<span data-ttu-id="9c0ed-157">サポートされているサインインの種類</span><span class="sxs-lookup"><span data-stu-id="9c0ed-157">What sign-in types are supported?</span></span>|<span data-ttu-id="9c0ed-158">UserPrincipalName + パスワード</span><span class="sxs-lookup"><span data-stu-id="9c0ed-158">UserPrincipalName + password</span></span><br><br><span data-ttu-id="9c0ed-159">[シームレス SSO](/azure/active-directory/hybrid/how-to-connect-sso) を使用した Windows 統合認証</span><span class="sxs-lookup"><span data-stu-id="9c0ed-159">Windows Integrated Authentication by using [Seamless SSO](/azure/active-directory/hybrid/how-to-connect-sso)</span></span><br><br>[<span data-ttu-id="9c0ed-160">代替ログイン ID</span><span class="sxs-lookup"><span data-stu-id="9c0ed-160">Alternate login ID</span></span>](/azure/active-directory/hybrid/how-to-connect-install-custom)|<span data-ttu-id="9c0ed-161">UserPrincipalName + パスワード</span><span class="sxs-lookup"><span data-stu-id="9c0ed-161">UserPrincipalName + password</span></span><br><br><span data-ttu-id="9c0ed-162">[シームレス SSO](/azure/active-directory/hybrid/how-to-connect-sso) を使用した Windows 統合認証</span><span class="sxs-lookup"><span data-stu-id="9c0ed-162">Windows Integrated Authentication by using [Seamless SSO](/azure/active-directory/hybrid/how-to-connect-sso)</span></span><br><br>[<span data-ttu-id="9c0ed-163">代替ログイン ID</span><span class="sxs-lookup"><span data-stu-id="9c0ed-163">Alternate login ID</span></span>](/azure/active-directory/hybrid/how-to-connect-pta-faq)|<span data-ttu-id="9c0ed-164">UserPrincipalName + パスワード</span><span class="sxs-lookup"><span data-stu-id="9c0ed-164">UserPrincipalName + password</span></span><br><br><span data-ttu-id="9c0ed-165">sAMAccountName + パスワード</span><span class="sxs-lookup"><span data-stu-id="9c0ed-165">sAMAccountName + password</span></span><br><br><span data-ttu-id="9c0ed-166">Windows 統合認証</span><span class="sxs-lookup"><span data-stu-id="9c0ed-166">Windows Integrated Authentication</span></span><br><br>[<span data-ttu-id="9c0ed-167">証明書とスマート カード認証</span><span class="sxs-lookup"><span data-stu-id="9c0ed-167">Certificate and smart card authentication</span></span>](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[<span data-ttu-id="9c0ed-168">代替ログイン ID</span><span class="sxs-lookup"><span data-stu-id="9c0ed-168">Alternate login ID</span></span>](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|<span data-ttu-id="9c0ed-169">Windows Hello for Business のサポート</span><span class="sxs-lookup"><span data-stu-id="9c0ed-169">Is Windows Hello for Business supported?</span></span>|[<span data-ttu-id="9c0ed-170">キー信頼モデル</span><span class="sxs-lookup"><span data-stu-id="9c0ed-170">Key trust model</span></span>](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[<span data-ttu-id="9c0ed-171">Intune での証明書信頼モデル</span><span class="sxs-lookup"><span data-stu-id="9c0ed-171">Certificate trust model with Intune</span></span>](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[<span data-ttu-id="9c0ed-172">キー信頼モデル</span><span class="sxs-lookup"><span data-stu-id="9c0ed-172">Key trust model</span></span>](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[<span data-ttu-id="9c0ed-173">Intune での証明書信頼モデル</span><span class="sxs-lookup"><span data-stu-id="9c0ed-173">Certificate trust model with Intune</span></span>](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[<span data-ttu-id="9c0ed-174">キー信頼モデル</span><span class="sxs-lookup"><span data-stu-id="9c0ed-174">Key trust model</span></span>](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[<span data-ttu-id="9c0ed-175">証明書信頼モデル</span><span class="sxs-lookup"><span data-stu-id="9c0ed-175">Certificate trust model</span></span>](/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|<span data-ttu-id="9c0ed-176">多要素認証のオプション</span><span class="sxs-lookup"><span data-stu-id="9c0ed-176">What are the multifactor authentication options?</span></span>|[<span data-ttu-id="9c0ed-177">Azure MFA</span><span class="sxs-lookup"><span data-stu-id="9c0ed-177">Azure MFA</span></span>](/azure/multi-factor-authentication/)<br><br>[<span data-ttu-id="9c0ed-178">条件付きアクセスを使用するカスタム コントロール\*</span><span class="sxs-lookup"><span data-stu-id="9c0ed-178">Custom Controls with conditional access\*</span></span>](/azure/active-directory/conditional-access/controls#custom-controls-1)|[<span data-ttu-id="9c0ed-179">Azure MFA</span><span class="sxs-lookup"><span data-stu-id="9c0ed-179">Azure MFA</span></span>](/azure/multi-factor-authentication/)<br><br>[<span data-ttu-id="9c0ed-180">条件付きアクセスを使用するカスタム コントロール\*</span><span class="sxs-lookup"><span data-stu-id="9c0ed-180">Custom Controls with conditional access\*</span></span>](/azure/active-directory/conditional-access/controls#custom-controls-1)|[<span data-ttu-id="9c0ed-181">Azure MFA</span><span class="sxs-lookup"><span data-stu-id="9c0ed-181">Azure MFA</span></span>](/azure/multi-factor-authentication/)<br><br>[<span data-ttu-id="9c0ed-182">Azure MFA サーバー</span><span class="sxs-lookup"><span data-stu-id="9c0ed-182">Azure MFA server</span></span>](/azure/active-directory/authentication/howto-mfaserver-deploy)<br><br>[<span data-ttu-id="9c0ed-183">サード パーティの MFA</span><span class="sxs-lookup"><span data-stu-id="9c0ed-183">Third-party MFA</span></span>](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[<span data-ttu-id="9c0ed-184">条件付きアクセスを使用するカスタム コントロール\*</span><span class="sxs-lookup"><span data-stu-id="9c0ed-184">Custom Controls with conditional access\*</span></span>](/azure/active-directory/conditional-access/controls#custom-controls-1)|
|<span data-ttu-id="9c0ed-185">サポートされるユーザー アカウントの状態</span><span class="sxs-lookup"><span data-stu-id="9c0ed-185">What user account states are supported?</span></span>|<span data-ttu-id="9c0ed-186">無効なアカウント</span><span class="sxs-lookup"><span data-stu-id="9c0ed-186">Disabled accounts</span></span><br><span data-ttu-id="9c0ed-187">(最大 30 分の遅延)</span><span class="sxs-lookup"><span data-stu-id="9c0ed-187">(up to 30-minute delay)</span></span>|<span data-ttu-id="9c0ed-188">無効なアカウント</span><span class="sxs-lookup"><span data-stu-id="9c0ed-188">Disabled accounts</span></span><br><br><span data-ttu-id="9c0ed-189">アカウントのロックアウト</span><span class="sxs-lookup"><span data-stu-id="9c0ed-189">Account locked out</span></span><br><br><span data-ttu-id="9c0ed-190">アカウント期限切れ</span><span class="sxs-lookup"><span data-stu-id="9c0ed-190">Account expired</span></span><br><br><span data-ttu-id="9c0ed-191">パスワード期限切れ</span><span class="sxs-lookup"><span data-stu-id="9c0ed-191">Password expired</span></span><br><br><span data-ttu-id="9c0ed-192">サインイン時間</span><span class="sxs-lookup"><span data-stu-id="9c0ed-192">Sign-in hours</span></span>|<span data-ttu-id="9c0ed-193">無効なアカウント</span><span class="sxs-lookup"><span data-stu-id="9c0ed-193">Disabled accounts</span></span><br><br><span data-ttu-id="9c0ed-194">アカウントのロックアウト</span><span class="sxs-lookup"><span data-stu-id="9c0ed-194">Account locked out</span></span><br><br><span data-ttu-id="9c0ed-195">アカウント期限切れ</span><span class="sxs-lookup"><span data-stu-id="9c0ed-195">Account expired</span></span><br><br><span data-ttu-id="9c0ed-196">パスワード期限切れ</span><span class="sxs-lookup"><span data-stu-id="9c0ed-196">Password expired</span></span><br><br><span data-ttu-id="9c0ed-197">サインイン時間</span><span class="sxs-lookup"><span data-stu-id="9c0ed-197">Sign-in hours</span></span>|
|<span data-ttu-id="9c0ed-198">条件付きアクセスのオプション</span><span class="sxs-lookup"><span data-stu-id="9c0ed-198">What are the conditional access options?</span></span>|[<span data-ttu-id="9c0ed-199">Azure AD 条件付きアクセス</span><span class="sxs-lookup"><span data-stu-id="9c0ed-199">Azure AD conditional access</span></span>](/azure/active-directory/active-directory-conditional-access-azure-portal)|[<span data-ttu-id="9c0ed-200">Azure AD 条件付きアクセス</span><span class="sxs-lookup"><span data-stu-id="9c0ed-200">Azure AD conditional access</span></span>](/azure/active-directory/active-directory-conditional-access-azure-portal)|[<span data-ttu-id="9c0ed-201">Azure AD 条件付きアクセス</span><span class="sxs-lookup"><span data-stu-id="9c0ed-201">Azure AD conditional access</span></span>](/azure/active-directory/active-directory-conditional-access-azure-portal)<br><br>[<span data-ttu-id="9c0ed-202">AD FS の要求規則</span><span class="sxs-lookup"><span data-stu-id="9c0ed-202">AD FS claim rules</span></span>](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|<span data-ttu-id="9c0ed-203">サポートされる従来のプロトコルのブロック</span><span class="sxs-lookup"><span data-stu-id="9c0ed-203">Is blocking legacy protocols supported?</span></span>|[<span data-ttu-id="9c0ed-204">はい</span><span class="sxs-lookup"><span data-stu-id="9c0ed-204">Yes</span></span>](/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[<span data-ttu-id="9c0ed-205">はい</span><span class="sxs-lookup"><span data-stu-id="9c0ed-205">Yes</span></span>](/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[<span data-ttu-id="9c0ed-206">はい</span><span class="sxs-lookup"><span data-stu-id="9c0ed-206">Yes</span></span>](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|<span data-ttu-id="9c0ed-207">サインイン ページのロゴ、イメージ、説明のカスタマイズ可能性</span><span class="sxs-lookup"><span data-stu-id="9c0ed-207">Can you customize the logo, image, and description on the sign-in pages?</span></span>|[<span data-ttu-id="9c0ed-208">Azure AD Premium を使用して可能</span><span class="sxs-lookup"><span data-stu-id="9c0ed-208">Yes, with Azure AD Premium</span></span>](/azure/active-directory/customize-branding)|[<span data-ttu-id="9c0ed-209">Azure AD Premium を使用して可能</span><span class="sxs-lookup"><span data-stu-id="9c0ed-209">Yes, with Azure AD Premium</span></span>](/azure/active-directory/customize-branding)|[<span data-ttu-id="9c0ed-210">はい</span><span class="sxs-lookup"><span data-stu-id="9c0ed-210">Yes</span></span>](/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|<span data-ttu-id="9c0ed-211">サポートされる高度なシナリオ</span><span class="sxs-lookup"><span data-stu-id="9c0ed-211">What advanced scenarios are supported?</span></span>|[<span data-ttu-id="9c0ed-212">Smart Password Lockout</span><span class="sxs-lookup"><span data-stu-id="9c0ed-212">Smart password lockout</span></span>](/azure/active-directory/active-directory-secure-passwords)<br><br>[<span data-ttu-id="9c0ed-213">漏洩した資格情報レポート</span><span class="sxs-lookup"><span data-stu-id="9c0ed-213">Leaked credentials reports</span></span>](/azure/active-directory/active-directory-reporting-risk-events)|[<span data-ttu-id="9c0ed-214">Smart Password Lockout</span><span class="sxs-lookup"><span data-stu-id="9c0ed-214">Smart password lockout</span></span>](/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|<span data-ttu-id="9c0ed-215">複数サイトの低待機時間の認証システム</span><span class="sxs-lookup"><span data-stu-id="9c0ed-215">Multisite low-latency authentication system</span></span><br><br>[<span data-ttu-id="9c0ed-216">AD FS エクストラネットのロックアウト</span><span class="sxs-lookup"><span data-stu-id="9c0ed-216">AD FS extranet lockout</span></span>](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[<span data-ttu-id="9c0ed-217">サード パーティの ID システムとの統合</span><span class="sxs-lookup"><span data-stu-id="9c0ed-217">Integration with third-party identity systems</span></span>](/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

<!-- markdownlint-enable MD033 -->

> [!NOTE]
> <span data-ttu-id="9c0ed-218">Azure AD の条件付きアクセスでのカスタム コントロールは、現時点ではデバイスの登録をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-218">Custom controls in Azure AD conditional access does not currently support device registration.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9c0ed-219">次の手順</span><span class="sxs-lookup"><span data-stu-id="9c0ed-219">Next steps</span></span>

<span data-ttu-id="9c0ed-220">[ハイブリッド ID デジタル変換フレームワーク](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?LCID=EN-US)に関する記事では、これらの各コンポーネントを選択および統合するためのさまざまな組み合わせとソリューションについて説明しています。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-220">The [Hybrid Identity Digital Transformation Framework](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?LCID=EN-US) outlines a number of combinations and solutions for choosing and integrating each of these components.</span></span>

<span data-ttu-id="9c0ed-221">[Azure AD Connect ツール](https://aka.ms/aadconnectwiz)は、オンプレミスのディレクトリを Azure AD と統合するために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9c0ed-221">The [Azure AD Connect tool](https://aka.ms/aadconnectwiz) helps you to integrate your on-premises directories with Azure AD.</span></span>