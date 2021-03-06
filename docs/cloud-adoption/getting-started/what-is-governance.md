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

# <a name="what-is-cloud-resource-governance"></a>クラウド リソース ガバナンスとは

[Azure のしくみ](what-is-azure.md)に関するページでは、Azure は、仮想化されたハードウェアとソフトウェアをユーザーの代わりに実行する、サーバーとネットワーク ハードウェアのコレクションであることを説明しました。 Azure を使用すると、必要に応じてリソースを容易に作成、読み取り、更新、および削除できるようにすることで、組織のアプリケーション開発および IT 部門の機敏性を高めることが可能です。

ただし、リソースに無制限にアクセスできるため、開発者は非常に機敏になり、予想外のコストが発生する可能性もあります。 たとえば、テスト用に一連のリソースのデプロイを承認された開発チームが、テスト完了後に、そのリソースを削除し忘れることがあります。 これらのリソースのコストは、承認が無効になったり不要になったりしても発生し続けます。

これを解決するのが、リソース アクセス ガバナンスです。 **ガバナンス**とは、組織の要件を満たすために、Azure リソースの使用を継続的に管理、監視、および監査するプロセスを意味します。

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ii94]

<!-- markdownlint-enable MD034 -->

これらの要件は組織によって異なるため、ガバナンスへの 1 つのアプローチでの対応は役立ちません。 代わりに、Azure の 2 つの主要ガバナンス ツール (**ロール ベースのアクセス制御 (RBAC)** と**リソース ポリシー**) を使用してガバナンス モデルを設計することは、それぞれの組織の責任です。

RBAC ではロールが定義され、そのロールによって、ロールに割り当てられている各ユーザーの機能が定義されます。 たとえば、**所有者**ロールでは、リソースのすべての機能 (作成、読み取り、更新、および削除) が許可されますが、**閲覧者**ロールでは、読み取り機能しか許可されません。 広範なスコープでロールを定義して、多くの種類のリソースに適用することも、スコープを狭くして適用対象を少なくすることもできます。

リソース ポリシーでは、リソース作成のルールが定義されます。 たとえば、リソース ポリシーによって、仮想マシンの SKU を、事前に承認された特定のサイズに制限できます。 リソースの作成が要求されたときに、別のリソース ポリシーによって、割り当てられたコスト センターに対するタグの適用を強制できます。

これらのツールを構成するときは、ガバナンスと組織の機敏性のバランスを保つことが重要です。 ガバナンス ポリシーの制限が厳しいほど、開発者や IT 作業者の機敏性が低下します。 ガバナンス ポリシーの制限が厳しいと、開発者がフォームを入力するか、ガバナンス チームのメンバーにメールして、リソースを手動で作成しなければならない、といった手動による操作が必要になる場合があります。 ガバナンス チームの能力には限りがあり、ボトルネックが発生する可能性があります。これにより、リソースが作成されるのを待っている開発チームの生産性が低下したり、リソースが削除される前に不要なリソースによりコストが発生したりします。

## <a name="next-steps"></a>次の手順

クラウド リソース ガバナンスの概念を理解したので、Azure でリソース アクセスを管理する方法について確認します。

> [!div class="nextstepaction"]
> [Azure でのリソース アクセスについて確認する](azure-resource-access.md)
