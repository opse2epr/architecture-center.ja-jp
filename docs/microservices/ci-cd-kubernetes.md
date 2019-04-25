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

# <a name="building-a-cicd-pipeline-for-microservices-on-kubernetes"></a>Kubernetes 上でのマイクロサービス用の CI/CD パイプラインの構築

マイクロサービス アーキテクチャ用に信頼性の高い CI/CD プロセスを作成するのは、困難な場合があります。 個々のチームは、他のチームを妨害したり、アプリケーション全体を不安定にしたりすることなく、サービスを迅速かつ確実にリリースできる必要があります。

この記事では、Azure Kubernetes Service (AKS) にマイクロサービスをデプロイするための CI/CD パイプラインの例を説明します。 すべてのチームやプロジェクトはそれぞれ異なるので、この記事を一連の厳格な規則としては考えないでください。 その代わり、独自の CI/CD プロセスを設計するための出発点になります。

ここで説明するパイプラインの例は、[GitHub][ri] 上で確認できるドローン配送アプリケーションと呼ばれるマイクロサービス参照実装のために作成されたものです。 アプリケーションのシナリオについては、[こちら](./design/index.md#reference-implementation)に記載されています。

パイプラインの目標は、次のようにまとめることができます。

- チームは、サービスを個別に構築およびデプロイすることができます。
- CI プロセスに合格したコード変更は、運用環境に似た環境に自動的にデプロイされます。
- 品質ゲートは、パイプラインの各段階で適用されます。
- サービスの新しいバージョンは、以前のバージョンと並行してデプロイできます。

背景の詳細については、[マイクロサービス アーキテクチャの CI/CD](./ci-cd.md) に関するページを参照してください。

## <a name="assumptions"></a>前提条件

この例の目的として、開発チームとコード ベースに関するいくつかの想定を次に示します。

- コード リポジトリは単一リポジトリであり、フォルダーがマイクロサービス別に整理されています。
- チームのブランチ戦略は、[トランクベース開発](https://trunkbaseddevelopment.com/)に基づいています。
- チームは、[リリース ブランチ](/azure/devops/repos/git/git-branching-guidance?view=azure-devops#manage-releases)を使用してリリースを管理します。 マイクロサービスごとに個別のリリースが作成されます。
- CI/CD プロセスでは、[Azure Pipelines](/azure/devops/pipelines/?view=azure-devops) を使用してマイクロサービスの AKS への構築、テスト、デプロイが行われます。
- 各マイクロサービスのコンテナー イメージは、[Azure Container Registry](/azure/container-registry/)に格納されます。
- チームは、Helm chart を使用して各マイクロサービスをパッケージ化します。

これらの想定によって、CI/CD パイプラインの特定の詳細の多くが設定されます。 ただし、ここで説明する基本的な手法は、Jenkins や Docker Hub など、他のプロセス、ツール、サービスにも適用されます。

## <a name="validation-builds"></a>検証ビルド

開発者は Delivery Service と呼ばれるマイクロサービスに取り組んでいるとします。 新機能の開発中に、開発者は、機能ブランチにコードをチェックインします。 慣例により、機能ブランチは `feature/*` と名付けられます。

![CI/CD ワークフロー](./images/aks-cicd-1.png)

ビルド定義ファイルには、次のように、ブランチ名とソース パスでフィルター処理するトリガーが含まれます。

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

&#11162; [ソース ファイル](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-validation.yml)を参照してください。

このアプローチを使用して、各チームは、専用のビルド パイプラインを持つことができます。 `/src/shipping/delivery` フォルダーにチェックインされているコードのみによって、Delivery Service のビルドがトリガーされます。 フィルターに一致するブランチにコミットをプッシュすると、CI のビルドがトリガーされます。 ワークフローのこの時点で、CI ビルドでは、いくつかの最小限のコードの検証が実行されます。

1. コードをビルドする。
1. 単体テストを実行する。

ビルド時間を短くして、開発者がすばやくフィードバックを取得できるようにすることが目標です。 開発者は、機能をマスターにマージする準備ができたら、PR を開きます。 これにより、いくつかの追加のチェックを実行する別の CI ビルドがトリガーされます。

1. コードをビルドする。
1. 単体テストを実行する。
1. ランタイム コンテナー イメージをビルドする。
1. イメージに対して脆弱性スキャンを実行する。

![CI/CD ワークフロー](./images/aks-cicd-2.png)

> [!NOTE]
> Azure DevOps Repos では、ブランチを保護するための[ポリシー](/azure/devops/repos/git/branch-policies)を定義できます。 たとえば、マスターにマージするには、CI ビルドの成功に加え、承認者のサインオフが必要であることをポリシーで要求できます。

## <a name="full-cicd-build"></a>完全な CI/CD ビルド

ある時点で、チームは、この Delivery サービスの新しいバージョンをデプロイする準備が整います。 リリース マネージャーで、`release/<microservice name>/<semver>` という名前付けパターンを使用して、マスターからブランチを作成します。 たとえば、「 `release/delivery/v1.0.2` 」のように入力します。

![CI/CD ワークフロー](./images/aks-cicd-3.png)

このブランチを作成すると、これまでのすべての手順に加え、以下を実行する完全な CI ビルドがトリガーされます。

1. Azure Container Registry にコンテナー イメージをプッシュする。 イメージには、ブランチ名から取得されたバージョン番号がタグ付けされます。
2. `helm package` を実行して、サービス用の Helm chart をパッケージ化する。 この chart にもバージョン番号がタグ付けされます。
3. Helm パッケージを Container Registry にプッシュする。

このビルドが成功したと仮定すると、Azure Pipelines の[リリース パイプライン](/azure/devops/pipelines/release/what-is-release-management)を使用するデプロイ (CD) プロセスがトリガーされます。 パイプラインには次の手順が含まれています。

1. Helm chart を QA 環境にデプロイする。
1. パッケージが運用環境に移動される前に、承認者がサインオフする。 「[Release deployment control using approvals (承認を使用したリリース デプロイ制御)](/azure/devops/pipelines/release/approvals/approvals)」をご覧ください。
1. Docker イメージに Azure Container Registry 内の運用名前空間のタグを付け直す。 たとえば、現在のタグが `myrepo.azurecr.io/delivery:v1.0.2` の場合、運用タグは `myrepo.azurecr.io/prod/delivery:v1.0.2` になります。
1. Helm chart を運用環境にデプロイする。

単一リポジトリであっても、これらのタスクのスコープを個々のマイクロサービスに設定できるため、チームは短時間でデプロイすることができます。 プロセスには、いくつか手動の手順があります。PR の承認、リリース ブランチの作成、および運用クラスターへのデプロイの承認です。 これらの手順はポリシーにより手動で行います。組織が望むのであれば、完全に自動化することもできます。

## <a name="isolation-of-environments"></a>環境の分離

開発、スモーク テスト、統合テスト、ロード テスト、最後に運用のための環境を含め、サービスをデプロイするための複数の環境が存在します。 これらの環境には、あるレベルの分離が必要です。 Kubernetes では、物理的な分離と論理的な分離のどちらかを選択できます。 物理的な分離は、個別のクラスターにデプロイすることを示します。 論理的な分離では、先に説明したように、名前空間とポリシーを使用します。

開発/テスト環境のための個別のクラスターと共に、専用の運用クラスターを作成することをお勧めします。 開発/テスト クラスター内の環境を分離するには、論理的な分離を使用します。 開発/テスト クラスターにデプロイされたサービスには、ビジネス データを保持するデータ ストアへのアクセス権を与えるべきではありません。

## <a name="build-process"></a>ビルド プロセス

可能であれば、ご自分のビルド プロセスを Docker コンテナーにパッケージ化します。 そうすれば、Docker を使用してご自分のコード成果物をビルドすることができるので、各ビルド コンピューター上でビルド環境を構成しなくても済みます。 ビルド プロセスのコンテナー化により、新しいビルド エージェントを追加して CI パイプラインをスケール アウトすることが容易になります。 また、チームの開発者はビルド コンテナーを実行するだけで、コードをビルドできます。

Docker 内でマルチ ステージ ビルドを使用すると、単一の Dockerfile 内にビルド環境とランタイム イメージを定義することができます。 たとえば、ASP.NET Core アプリケーションをビルドする Dockerfile を次に示します。

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

&#11162; [ソース ファイル](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/workflow/Dockerfile)を参照してください。

この Dockerfile では、いくつかのビルド ステージが定義されます。 `base` という名前のステージでは ASP.NET Core ランタイムが使用されていますが、`build` という名前のステージでは完全な ASP.NET Core SDK が使用されていることに注目してください。 `build` ステージは、ASP.NET Core プロジェクトをビルドするために使用されます。 しかし、最終的なランタイム コンテナーは `base` から作成されます。これは、ランタイムしか含まないので、完全な SDK イメージよりかなり小さくなっています。

### <a name="building-a-test-runner"></a>テスト ランナーのビルド

もう 1 つのおすすめできる方法は、コンテナー内で単体テストを実行することです。 たとえば、次に示すのは、テスト ランナーをビルドする Docker ファイルの一部です。

```
FROM build AS testrunner
WORKDIR /src/tests

COPY Fabrikam.Workflow.Service.Tests/*.csproj .
RUN dotnet restore Fabrikam.Workflow.Service.Tests.csproj

COPY Fabrikam.Workflow.Service.Tests/. .
ENTRYPOINT ["dotnet", "test", "--logger:trx"]
```

開発者は、この Docker ファイルを使用することで、テストをローカルで実行することができます。

```bash
docker build . -t delivery-test:1 --target=testrunner
docker run -p 8080:8080 delivery-test:1
```

また、CI パイプラインでは、ビルド検証手順の一環としてテストが実行される必要があります。

このファイルでは Docker の `RUN` コマンドではなく、Docker の `ENTRYPOINT` コマンドが使用されていることに注目してください。

- `RUN` コマンドを使用した場合は、イメージをビルドするたびにテストが実行されます。 `ENTRYPOINT` を使用することにより、テストはオプトインになります。 `testrunner` ステージを明示的にターゲットとした場合に限り、テストが実行されます。
- テストの失敗により、Docker の `build` コマンドが失敗することはありません。 このようにして、コンテナー ビルドの失敗をテストの失敗と区別することができます。
- テストの結果は、マウントされたボリュームに保存できます。

### <a name="container-best-practices"></a>コンテナーのベスト プラクティス

コンテナーについて考慮すべきその他のベスト プラクティスを次に示します。

- クラスター (ポッドやサービスなど) にデプロイされるリソースのコンテナー タグ、バージョン管理、および名前付けに関する組織規模の規則を定義します。 これにより、デプロイに関する問題が診断しやすくなります。

- 開発とテストのサイクル中、CI/CD 処理は、多数のコンテナー イメージをビルドします。 それらのイメージの一部のみがリリース候補になり、さらにそれらのリリース候補の一部のみが運用環境に昇格されます。 明確なバージョン管理戦略を用意してください。それによって、どのバージョンが現在運用環境にデプロイされているかを把握し、必要な場合に前のバージョンにロールバックすることができます。

- `latest` ではなく、特定のバージョン タグを常にデプロイしてください。

- Azure Container Registry 内の[名前空間](/azure/container-registry/container-registry-best-practices#repository-namespaces)を使用して、運用するために承認されたイメージを、まだテスト中であるイメージから分離します。 運用環境にデプロイする準備ができるまで、イメージを運用名前空間に移動しないでください。 このプラクティスとコンテナー イメージのセマンティックなバージョン管理を組み合わせると、リリースを承認されていないバージョンが誤ってデプロイされる可能性を低くすることができます。

- コンテナーを特権のないユーザーとして実行して、最小特権の原則に従います。 Kubernetes では、コンテナーが "*ルート*" として実行されないように、ポッド セキュリティ ポリシーを作成することができます。 [ポッドがルート権限で実行されるのを防止](https://docs.bitnami.com/kubernetes/how-to/secure-kubernetes-cluster-psp/)に関するページを参照してください。

## <a name="helm-charts"></a>Helm チャート

Helm を使用してサービスの構築やデプロイを管理することを考慮してください。 CI/CD に役立つ Helm の機能のいくつかを次に示します。

- 多くの場合、単一のマイクロサービスは、複数の Kubernetes オブジェクトによって定義されます。 Helm を使用すると、これらのオブジェクトを 1 つの Helm chart にパッケージ化できます。
- 一連の kubectl コマンドではなく、1 つの Helm コマンドを使用して chart をデプロイできます。
- グラフは明示的にバージョン管理されます。 Helm を使用して、バージョンのリリース、リリースの表示、および以前のバージョンへのロールバックを行います。 以前のバージョンにロールバックする機能と共にセマンティック バージョニングを使用して、更新プログラムやリビジョンを追跡します。
- Helm chart では、多数のファイルにわたる情報の複製 (ラベルやセレクターなど) を回避するためにテンプレートが使用されます。
- Helm では、chart 間の依存関係を管理できます。
- chart を Helm リポジトリ (Azure Container Registry など) に格納して、ビルド パイプラインに統合することができます。

Helm リポジトリとしての Container Registry の使用の詳細については、「[アプリケーションのグラフに Helm リポジトリとして Azure Container Registry を使用する](/azure/container-registry/container-registry-helm-repos)」を参照してください。

1 つのマイクロサービスに、複数の Kubernetes 構成ファイルが必要となる場合があります。 サービスの更新は、セレクター、ラベル、およびイメージのタグを更新するためにこれらのファイルに手を加えることを意味する場合があります。 Helm では、これらが chart と呼ばれる 1 つのパッケージとして扱われ、変数を使用することで YAML ファイルを簡単に更新することができます。 Helm では、パラメーター化された YAML 構成ファイルを記述できるようにするために、(Go テンプレートに基づく) テンプレート言語が使用されます。

たとえば、デプロイを定義する YAML ファイルの一部を次に示します。

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

&#11162; [ソース ファイル](https://github.com/mspnp/microservices-reference-implementation/blob/master/charts/package/templates/package-deploy.yaml)を参照してください。

デプロイ名、ラベル、およびコンテナー仕様のすべてで、テンプレート パラメーターが使用されていることがわかります。これらはデプロイ時に指定されます。 たとえば、コマンドラインから次を実行します。

```bash
helm install $HELM_CHARTS/package/ \
     --set image.tag=0.1.0 \
     --set image.repository=package \
     --set dockerregistry=$ACR_SERVER \
     --namespace backend \
     --name package-v0.1.0
```

ご利用の CI/CD パイプラインで、直接、Kubernetes に chart をインストール可能であるとしても、chart アーカイブ (.tgz ファイル) を作成して、chart を Azure Container Registry などの Helm リポジトリにプッシュすることをお勧めします。 詳細については、「[Package Docker-based apps in Helm charts in Azure Pipelines](/azure/devops/pipelines/languages/helm?view=azure-devops)」 (Azure パイプライン内で Docker ベースのアプリを Helm charts にパッケージ化する) を参照してください。

Helm を独自の名前空間にデプロイすること、およびロールベースのアクセス制御 (RBAC) を使用して Helm をデプロイできる名前空間を制限することを検討してください。 詳細については、Helm のドキュメント内の[ロールベースのアクセス制御](https://helm.sh/docs/using_helm/#helm-and-role-based-access-control)に関するページを参照してください。

### <a name="revisions"></a>リビジョン

Helm chart には常にバージョン番号が用意されます。これには[セマンティック バージョニング](https://semver.org/)が使用される必要があります。 chart には `appVersion` も用意されます。 このフィールドはオプションであり、chart バージョンに関連付ける必要はありません。 チームによっては、chart に対する更新プログラムとは別にアプリケーション バージョンを必要とする場合があります。 しかし、よりシンプルな方法は、1 つのバージョン番号を使用することです。結果、chart バージョンとアプリケーション バージョンの間の関係は 1:1 になります。 そうすれば、リリースごとに 1 つの chart を格納して、必要なリリースを簡単にデプロイすることができます。

```bash
helm install <package-chart-name> --version <desiredVersion>
```

もう 1 つ、次のようにデプロイ テンプレート内に change-cause 注釈を指定することをお勧めします。

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

これにより、`kubectl rollout history` コマンドを使用して、リビジョンごとに change-cause フィールドを表示することができます。 前の例では、change-cause は Helm chart のパラメーターとして指定されました。

```bash
$ kubectl rollout history deployments/delivery-v010 -n backend
deployment.extensions/delivery-v010
REVISION  CHANGE-CAUSE
1         Initial deployment
```

`helm list` コマンドを使用して、次のようにリビジョン履歴を表示することもできます。

```bash
~$ helm list
NAME            REVISION    UPDATED                     STATUS        CHART            APP VERSION     NAMESPACE
delivery-v0.1.0 1           Sun Apr  7 00:25:30 2019    DEPLOYED      delivery-v0.1.0  v0.1.0          backend
```

> [!TIP]
> Helm を初期化する際は、`--history-max` フラグを使用します。 この設定により、Tiller がその履歴内に保存するリビジョン数が制限されます。 Tiller では configmaps 内にリビジョン履歴が格納されます。 更新プログラムを頻繁にリリースしている場合、履歴のサイズを制限しないと、configmaps のサイズは非常に大きくなる可能性があります。

## <a name="azure-devops-pipeline"></a>Azure DevOps パイプライン

Azure Pipeline では、パイプラインは*ビルド パイプライン*と*リリース パイプライン*に分割されます。 ビルド パイプラインでは、CI プロセスが実行され、ビルド成果物が作成されます。 Kubernetes 上のマイクロサービス アーキテクチャの場合、これらの成果物は、各マイクロサービスを定義するコンテナー イメージと Helm chart です。 リリース パイプラインでは、その CD プロセスが実行され、それによってマイクロサービスがクラスターにデプロイされます。

この記事で前述した CI フローに基づいて、ビルド パイプラインは次のタスクで構成される可能性があります。

1. テスト ランナー コンテナーをビルドします。

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        arguments: '--pull --target testrunner'
        dockerFile: $(System.DefaultWorkingDirectory)/$(dockerFileName)
        imageName: '$(imageName)-test'
    ```

1. テスト ランナー コンテナーに対して docker run を呼び出すことによって、テストを実行します。

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

1. テスト結果を公開します。 「[Integrate build and test tasks](/azure/devops/pipelines/languages/docker?view=azure-devops&tabs=yaml#integrate-build-and-test-tasks)」 (タスクの統合、ビルドおよびテスト) を参照してください。

    ```yaml
    - task: PublishTestResults@2
      inputs:
        testResultsFormat: 'VSTest'
        testResultsFiles: 'TestResults/*.trx'
        searchFolder: '$(System.DefaultWorkingDirectory)'
        publishRunAttachments: true
    ```

1. ランタイム コンテナーをビルドします。

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        dockerFile: $(System.DefaultWorkingDirectory)/$(dockerFileName)
        includeLatestTag: false
        imageName: '$(imageName)'
    ```

1. Azure Container Registry (またはその他のコンテナー レジストリ) にコンテナーをプッシュします。

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        command: 'Push an image'
        imageName: '$(imageName)'
        includeSourceTags: false
    ```

1. Helm chart をパッケージ化します。

    ```yaml
    - task: HelmDeploy@0
      inputs:
        command: package
        chartPath: $(chartPath)
        chartVersion: $(Build.SourceBranchName)
        arguments: '--app-version $(Build.SourceBranchName)'
    ```

1. Azure Container Registry (またはその他の Helm リポジトリ) に Helm パッケージをプッシュします。

    ```yaml
    task: AzureCLI@1
      inputs:
        azureSubscription: $(AzureSubscription)
        scriptLocation: inlineScript
        inlineScript: |
        az acr helm push $(System.ArtifactsDirectory)/$(repositoryName)-$(Build.SourceBranchName).tgz --name $(AzureContainerRegistry);
    ```

&#11162; [ソース ファイル](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-ci.yml)を参照してください。

CI パイプラインからの出力は、実稼働可能なコンテナー イメージと、マイクロサービスの更新された Helm chart です。 この時点では、リリース パイプラインに引き継ぐことができます。 それによって次の手順が実行されます。

- 開発、QA、ステージング環境にデプロイする。
- 承認者がデプロイを承認または却下するのを待機する。
- コンテナー イメージにリリースのためのタグを付け直す
- リリース タグをコンテナー レジストリにプッシュする。
- 運用環境クラスター内の Helm chart をアップグレードする。

リリース パイプラインの作成の詳細については、「[Release pipelines, draft releases, and release options](/azure/devops/pipelines/release/?view=azure-devops)」 (リリース パイプライン、ドラフト リリース、およびリリース オプション) を参照してください。

次の図に、この記事で説明したエンドツーエンドの CI/CD プロセスを示します。

![CD/CD パイプライン](./images/aks-cicd-flow.png)

## <a name="next-steps"></a>次の手順

この記事は、[GitHub][ri] 上で確認できる参照実装に基づいています。

[ri]: https://github.com/mspnp/microservices-reference-implementation