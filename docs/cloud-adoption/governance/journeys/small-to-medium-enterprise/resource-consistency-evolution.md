---
title: 'CAF: 中小企業 – リソース整合性の進化'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 中小企業 – リソース整合性の進化
author: BrianBlanchard
ms.openlocfilehash: caacd62da9da526ec01ab935896598065f64d9d1
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58246503"
---
# <a name="small-to-medium-enterprise-resource-consistency-evolution"></a>中小企業: リソース整合性の進化

この記事では、物語をさらに進化させて、ミッション クリティカルなアプリケーションをサポートするためのリソース整合性のコントロールを追加します。

## <a name="evolution-of-the-narrative"></a>物語の進化

新しい顧客エクスペリエンス、新しい予測ツール、そして移行されたインフラストラクチャは、前進を続けます。 これらの資産を、本番容量で業務に使用できる状態になりました。

### <a name="evolution-of-the-current-state"></a>現在の状態の進化

この物語の前のフェーズでは、アプリケーション開発と BI のチームが顧客と財務のデータを本番ワークロードに統合する準備がほぼ完了していました。 IT チームは、DR データベースの廃止作業を行っているところでした。

それ以来、次のようなことが変化していますが、これらはガバナンスに影響します。

- IT チームは DR データセンターの 100% 廃止を、予定よりも前に完了しました。 その過程で、実稼働データセンターにあった多数の資産がクラウド移行候補として特定されました。
- アプリケーション開発チームは、本番トラフィックに対応できる状態になっています。
- BI チームは、予測と分析情報を実稼働データセンター内のオペレーション システムに返せる状態になっています。

### <a name="evolution-of-the-future-state"></a>将来の状態の進化

Azure のデプロイを本番の業務プロセスで使用するには、クラウド運用が成熟している必要があります。 併せて、資産を適切に運用するためのさらなるガバナンス進化が必要です。

現在や将来の状態が変わると、新しいリスクが現れるため、新しいポリシー ステートメントが必要になります。

## <a name="evolution-of-tangible-risks"></a>具体的なリスクの進化

**ビジネスの中断**: 新しいプラットフォームにはつきもののリスクがあり、ミッション クリティカルなビジネス プロセスの中断の原因となります。 IT 運用チームと、さまざまなクラウド導入を実施するチームは、クラウド運用の経験がそれほどありません。 このことは中断のリスクを増大させるため、緩和とガバナンスが必要です。

このビジネス リスクが拡大して、次のような多数の技術的リスクとなる可能性があります。

- 外部からの侵入またはサービス拒否攻撃により、ビジネスが中断させられる可能性があります。
- ミッション クリティカルな資産が適切に検出されないことがあり、その結果として適切に運用されなくなる可能性があります。
- 未検出やラベル誤りのある資産が、既存の運用管理プロセスでサポートされない可能性があります。
- デプロイされた資産の構成が、期待どおりのパフォーマンスを達成しないことがあります。
- ログへの記録が正しく行われず、集中管理もされていないため、パフォーマンスの問題の修復に対応できなくなる可能性があります。
- 復旧ポリシーが守られない、または予想よりも長い時間がかかる可能性があります。
- 一貫性のないデプロイ プロセスによってセキュリティの間隙が発生し、それがデータ流出または中断につながる可能性があります。
- 構成ドリフトやパッチの適用漏れによって意図しないセキュリティの間隙が発生し、それがデータ流出または中断につながる可能性があります。
- 定義済みの SLA の要件や確約済みの復旧要件を構成が満たすことができない可能性があります。
- デプロイ済みのオペレーティング システムやアプリケーションがセキュリティ強化の要件を満たせない可能性があります。
- 多数のチームがクラウドで作業しているため、不整合のリスクがあります。

## <a name="evolution-of-the-policy-statements"></a>ポリシー ステートメントの進化

ポリシーに対する次の変更は、新しいリスクを軽減して実装を導くのに役立ちます。 多すぎるように見えるかもしれませんが、これらのポリシーの採用はそれほど難しいものではありません。

1. デプロイされたすべての資産を、重要性とデータ機密性によって分類する必要があります。 機密性の分類は、クラウドへのデプロイ前に、クラウド ガバナンス チームとアプリケーション所有者によってレビューされることになります。
2. ミッション クリティカルなアプリケーションが存在するサブネットをファイアウォール ソリューションで保護します。このソリューションは、侵入の検知と攻撃への対応ができることが必要です。
3. セキュリティ管理チームによって定義されたネットワーク構成要件をガバナンス ツールで監査し、強制する必要があります。
4. ミッション クリティカルなアプリケーションや保護されるデータに関連するすべての資産がリソース枯渇と最適化の監視対象となっていることを、ガバナンス ツールで検証する必要があります。
5. ミッション クリティカルなアプリケーションや保護されるデータのすべてについて適切なレベルのログ データが収集されていることを、ガバナンス ツールで検証する必要があります。
6. ミッション クリティカルなアプリケーションと保護されるデータのためのバックアップ、復旧、SLA 準拠が正しく実装されていることを、ガバナンス プロセスで検証する必要があります。
7. 仮想マシンのデプロイは承認されたイメージのみとなるように、ガバナンス ツールで制限する必要があります。
8. ミッション クリティカルなアプリケーションをサポートするデプロイ済み資産のすべてについて自動更新が禁止されていることを、ガバナンス ツールで強制する必要があります。 違反は運用管理チームがレビューし、運用ポリシーに従って修復する必要があります。 自動的に更新されない資産は、IT 運用チームが所有者となっているプロセスに含める必要があります。
9. コスト、重要性、SLA、アプリケーション、データ分類に関連するタグ付けをガバナンス ツールで検証する必要があります。 すべての値は、ガバナンス チームが管理する事前定義済みの値と整合している必要があります。
10. すべての資産にわたって一貫性を保証するために、デプロイ時点と定期的なサイクルでの監査をガバナンス プロセスに含める必要があります。
11. クラウドのデプロイに影響を及ぼす可能性がある傾向と悪用をセキュリティ チームが定期的にレビューします。これに基づいて、クラウドで使用されるセキュリティ管理ツールを更新します。
12. 本番へのリリースの前に、すべてのミッション クリティカルなアプリケーションと保護されるデータを所定の運用監視ソリューションに追加する必要があります。 選択した IT 運用ツールで検出できない資産は、本番用としてリリースすることはできません。 資産を検出可能にするために必要な変更を、関係するデプロイ プロセスに対して行う必要があります。将来のデプロイで資産が確実に検出可能となるようにするためです。
13. 検出時に、運用管理チームは資産のサイズを調整します。その資産が確実にパフォーマンス要件を満たすようにするためです。
14. デプロイ ツールはクラウド ガバナンス チームの承認を受ける必要があります。これは、デプロイ済み資産の継続的なガバナンスを確実にするためです。
15. デプロイ スクリプトは、中央リポジトリに保持し、クラウド ガバナンス チームが定期的なレビューと監査のためにアクセスできるようにする必要があります。
16. デプロイ済みの資産が SLA と復旧の要件に合わせて適切に構成されていることを、ガバナンス レビュー プロセスで検証する必要があります。

## <a name="evolution-of-the-best-practices"></a>ベスト プラクティスの進化

記事のこのセクションでは、ガバナンス MVP の設計を進化させて、新しい Azure ポリシーと Azure Cost Management の実装を含めます。 この 2 つの設計変更が一体となって、会社の新しいポリシー ステートメントの内容を実現します。

1. クラウド運用チームは、運用監視と自動修復に使用するツールを定義します。 クラウド ガバナンス チームは、これらの検出プロセスをサポートします。 このユース ケースでは、クラウド運用チームは Azure Monitor をミッション クリティカルなアプリケーションの監視の主要ツールとして選択します。
2. Azure DevOps でリポジトリを作成し、関連するすべての Resource Manager テンプレートとスクリプト化された構成をここに保存してバージョン管理を行います。
3. Azure バックアップ コンテナーの実装:
    1. バックアップと回復のプロセス用の Azure コンテナーを定義し、デプロイします。
    2. コンテナーを各サブスクリプションに作成するための Resource Management テンプレートを作成します。
4. すべてのサブスクリプションについて Azure Policy を次のように更新します。
    1. すべてのサブスクリプションを対象として重要性とデータ分類を監査し、強制します。これは、ミッション クリティカルな資産を持つサブスクリプションを特定するためです。
    2. 承認されたイメージだけを使用することを監査し、強制します。
5. Azure Monitor の実装:
    1. ミッション クリティカルなサブスクリプションが特定されたら、PowerShell を使用して Azure Monitor のワークスペースを作成します。 これは、デプロイ前のプロセスです。
    2. デプロイ テストの中で、クラウド運用チームは必要なエージェントをデプロイして検出をテストします。
6. ミッション クリティカルなアプリケーションを持つすべてのサブスクリプションについて、Azure Policy を更新します。
    1. 1 つの NSG をすべての NIC とサブネットに適用することを監査し、強制します。 ネットワーク構成と IT セキュリティによって NSG が定義されます。
    2. ネットワーク インターフェイスのそれぞれについて、承認済みのネットワーク サブネットと VNet を使用することを監査し、強制します。
    3. ユーザー定義のルーティング テーブルの制限を監査し、強制します。
    4. すべての仮想マシンに対して Azure Monitor エージェントをデプロイすることを監査し、強制します。
    5. Azure コンテナーがサブスクリプションに存在することを監査し、強制します。
7. ファイアウォールの構成:
    1. セキュリティの要件を満たす Azure Firewall の構成を特定します。 または、Azure と連携するサードパーティ製アプライアンスを特定します。
    2. 必須の構成を持つファイアウォールをデプロイするための Resource Manager テンプレートを作成します。
8. Azure ブループリント:
    1. `protected-data` という名前の Azure ブループリントを作成します。
    2. ファイアウォールと Azure コンテナーのテンプレートをブループリントに追加します。
    3. 保護対象データのサブスクリプション用の新しいポリシーを追加します。
    4. このブループリントを、ミッション クリティカルなアプリケーションをホストする予定の管理グループに発行します。
    5. 新しいブループリントを、影響を受けるサブスクリプションのそれぞれに既存のブループリントとともに適用します。

## <a name="conclusion"></a>まとめ

これらのプロセスと変更をガバナンス MVP に追加することは、リソース ガバナンスに伴うリスクの多くの軽減に役立ちます。 これらが一体となって追加される復旧、サイズ調整、監視のコントロールは、クラウドに対応した運用の力となります。

## <a name="next-steps"></a>次の手順

クラウド導入が進化を続けてさらなるビジネス価値をもたらすのと同時に、リスクとクラウド ガバナンスのニーズも進化します。 この体験の架空の会社の場合、次の契機となるのは、クラウドにデプロイされる資産が 100 を超える規模になったとき、または月間の支出額が 1,000 米ドルを超えたときです。 この時点で、クラウド ガバナンス チームはコスト管理のコントロールを追加します。

> [!div class="nextstepaction"]
> [コスト管理の進化](./cost-management-evolution.md)