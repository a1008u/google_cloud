## セクション 1. クラウド ソリューション環境の設定
### 1.1 クラウド プロジェクトとアカウントを設定する。次のような流れになります。

#### a. リソース階層の作成
#### b. リソース階層への組織のポリシーの適用
#### c. プロジェクト内の IAM ロールをメンバーに付与する
#### d. Cloud Identity でのユーザーとグループの管理（手動および自動）
#### e. プロジェクトで API を有効にする
#### f. Google Cloud のオペレーション スイートでのプロダクトのプロビジョニングと設定

### 1.2 課金構成を管理する。次のような流れになります。

#### a. 請求先アカウントを作成する
#### b. プロジェクトを請求先アカウントにリンクする
#### c. 課金の予算とアラートを設定する
#### d. 課金データのエクスポートの設定

### 1.3 コマンドライン インターフェース（CLI）、具体的には Cloud SDK をインストールして構成する（デフォルト プロジェクトの設定など）。

## セクション 2. クラウド ソリューションの計画と構成

### 2.1 料金計算ツールを使用して Google Cloud プロダクトの使用量の計画と見積もりを作成する

### 2.2 コンピューティング リソースを計画し、構成する。 以下のような点を考慮します。

#### a. ワークロードに適したコンピューティング サービスを選択する（Compute Engine、Google Kubernetes Engine、Cloud Run、Cloud Functions など）
#### b. 必要に応じてプリエンプティブル VM とカスタム マシンタイプを使用する

### 2.3 データ ストレージ オプションを計画し、構成する。 以下のような点を考慮します。

#### a. プロダクトの選択（Cloud SQL、BigQuery、Firestore、Cloud Spanner、Cloud Bigtable など）
#### b. ストレージ オプションの選択（ゾーン永続ディスク、リージョン バランス永続ディスク、標準、Nearline、Coldline、Archive など）

### 2.4 ネットワーク リソースを計画し、構成する。以下のようなタスクを行います。

#### a. 負荷分散オプションの違いを見分ける
#### b. 可用性を考慮してネットワーク内のリソースのロケーションを決定する
#### c. Cloud DNS の構成


## セクション 3. クラウド ソリューションのデプロイと実装

### 3.1 Compute Engine リソースをデプロイし、実装する。以下のようなタスクを行います。

#### a. Google Cloud コンソールと Cloud SDK（gcloud）を使用してCompute インスタンスを起動する（例: ディスクの割り当て、可用性ポリシー、SSH 認証鍵など）
#### b. インスタンス テンプレートを使用して、自動スケーリングされるマネージド インスタンス グループを作成する
#### c. インスタンス用のカスタム SSH 認証鍵の生成 / アップロード
#### d. Cloud Monitoring と Cloud Logging のエージェントをインストールし、構成する
#### e. コンピューティングの割り当てを評価し、割り当ての増加をリクエストする
#### f. アクセススコープについて
[アクセススコープ](https://cloud.google.com/compute/docs/access/service-accounts#accesscopesiam)
> アクセス スコープは、インスタンスの認証を指定するレガシーな方法です。これらは、gcloud CLI またはクライアント ライブラリからのリクエストで使用されるデフォルトの OAuth スコープを定義します

- `gce`の場合`gcloud`や`api`に`gce`経由でアクセスする場合には、サービスアカウントとアクセススコープの両方の設定が必要。
- 使用可能なアクセス スコープは多数ありますが、ベスト プラクティスは cloud-platform アクセス スコープを設定することです。
    これは、ほとんどの Google Cloud の OAuth スコープです。サービス アカウントに IAM ロールを付与し、サービス アカウントのアクセス権を制御できます。
    https://www.googleapis.com/auth/cloud-platform

### 3.2 Google Kubernetes Engine リソースをデプロイし、実装する。以下のようなタスクを行います。

#### a. Kubernetes 用のコマンドライン インターフェース（CLI）である kubectl をインストールして構成する
#### b. AutoPilot、リージョン クラスタ、限定公開クラスタなど、さまざまな構成で Google Kubernetes Engine クラスタをデプロイする
#### c. Google Kubernetes Engine にコンテナ化したアプリケーションをデプロイする
#### d. Google Kubernetes Engine のモニタリングとロギングを構成する

### 3.3 Cloud Run リソース、Cloud Functions リソースをデプロイし、実装する。以下のようなタスクがあります（該当する場合）。

#### a. アプリケーションをデプロイし、スケーリング構成、バージョン、トラフィック分割を更新する
#### b. Google Cloud イベント（Pub/Sub イベント、Cloud Storage オブジェクト変更通知イベントなど）を受け取るアプリケーションをデプロイする

### 3.4 データ ソリューションをデプロイし、実装する。以下のようなタスクを行います。

#### a. 製品を使用してデータシステムを初期化する（例:Cloud SQL、Firestore、BigQuery、Cloud Spanner、Pub/Sub、Cloud Bigtable、Dataproc、Dataflow、Cloud Storage など）
#### b. データを読み込む（コマンドラインによるアップロード、API による転送、インポート / エクスポート、Cloud Storage からのデータの読み込み、Cloud Pub/Sub へのデータのストリーミングなど）

### 3.5 ネットワーキング リソースをデプロイし、実装する。 以下のようなタスクを行います。

#### a. サブネットを持つ VPC を作成する（カスタムモード VPC、共有 VPC など）
VPCはグローバルリソースです。
[[CLI]gcloud compute networks create](https://cloud.google.com/sdk/gcloud/reference/compute/networks/create)

``` shell
# --subnet-mode=auto => Subnets are created automatically. This is the recommended selection.
gcloud compute networks create ace-exam-vpc1 --subnet-mode=auto
#  --subnet-mode=custom => Create subnets manually.
gcloud compute networks create ace-exam-vpc1 --subnet-mode=custom

gcloud beta compute networks subnets create ace-exam-vpc-subnet1 \
--network=ace- exam-vpc1 --region=us-west2 --range=10.10.0.0/16 \
--enable-private-ip-google- access --enable-flow-logs
```

共有VPC

- 共有VPCを作成するコマンドを実行する前に、組織メンバーに対して組織レベルまたはフォルダレベルでShared VPC Adminのロールを割り当てる必要があります。
- Shared VPC Adminロールを割り当てるには、記述子roles/compute.xpnAdminを使用し、次のコマンドを実行します。

``` shell
# 事前準備
gcloud organizations add-iam-policy-binding [ORG_ID] --member='user:[EMAIL_ADDRESS]' --role="roles/compute.xpnAdmin"
gcloud beta resource-manager folders add-iam-policy-binding [FOLDER_ID] --member='user:[EMAIL_ADDRESS]' --role="roles/compute.xpnAdmin"
gcloud beta resource-manager folders list --organization=[ORG_ID]

# 共有VPC作成
gcloud compute shared-vpc enable [HOST_PROJECT_ID]
gcloud beta compute shared-vpc enable [HOST_PROJECT_ID]

# HOSTと紐づけたいプロジェクトの紐付け
gcloud compute shared-vpc associated-projects add [SERVICE_PROJECT_ID] --host-project [HOST_PROJECT_ID]
gcloud beta compute shared-vpc associated-projects add [SERVICE_PROJECT_ID] --host-project [HOST_PROJECT_ID]

```

VPC peering

- 2つのVPCをピアリングするには、それぞれのネットワークにpeeringを指定します。

``` shell
# ace-exam-network-Aの設定
gcloud compute networks peerings create peer-ace-exam-1 \
--network ace-exam-network-A \
--peer-project ace-exam-project-B \
--peer-network ace-exam-network-B \
--auto-create-routes

# ace-exam-network-Bの設定
gcloud compute networks peerings create peer-ace-exam-1 \
--network ace-exam-network-B \
--peer-project ace-exam-project-A \
--peer-network ace-exam-network-A \
--auto-create-routes
```

#### b. カスタム ネットワーク構成を持つ Compute Engine インスタンスを起動する（内部専用 IP アドレス、限定公開の Google アクセス、静的外部 IP アドレスとプライベート IP アドレス、ネットワーク タグなど）

``` shell
gcloud compute instances create [INSTANCE_NAME] --subnet [SUBNET_NAME] --zone [ZONE_NAME]
```

#### c. VPC 用の上り（内向き）および下り（外向き）ファイアウォール ルール（例: IP サブネット、ネットワーク タグ、サービス アカウント）を作成する
``` shell
gcloud compute firewall-rules create ace-exam-fwr2 –-network ace-exam-vpc1 –-allow tcp:20000-25000
```
#### d. Cloud VPN を使用して Google VPC と外部ネットワークとの間の VPN を作成する

- [[事例]GCP に VPC を構築して Cloud VPN で自宅ラボネットワークと VPN 接続](https://qiita.com/suzuyui/items/cfea1dc3d51a2e3be462)
- [[事例]Google Cloud （GCP）でVPNを構築してAWS、Azureと通信する方法をご紹介！](hhttps://www.topgate.co.jp/gcp-vpn)
- [[事例]GCPサイト間VPNの構築（6.gcloud CLI によるVPN接続の作成）](https://infrastructure-engineer.com/gcp-site-to-site-vpn-006/)


``` shell
gcloud compute vpn-gateways create GW_NAME_1 --network=NETWORK_1 --region=REGION_1 --stack-type=IP_STACK
gcloud compute forwarding-rules create NAME --TARGET_SPECIFICATION=VPN_GATEWAY
gcloud compute vpn-tunnels create NAME --peer-address=PEER_ADDRESS --shared-secret=SHARED_SECRET --target-vpn-gateway=TARGET_VPN_GATEWAY
```

#### e. アプリケーションへのネットワーク トラフィックを分散するロードバランサの作成（グローバル HTTP(S) ロードバランサ、グローバル SSL プロキシ ロードバランサ、グローバル TCP プロキシ ロードバランサ、リージョン ネットワーク ロードバランサ、リージョン内部ロードバランサなど）

### 3.6 Cloud Marketplace を使用してソリューションをデプロイする。以下のようなタスクを行います。

#### a. Cloud Marketplace カタログを閲覧し、ソリューションの詳細を見る
#### b. Cloud Marketplace ソリューションをデプロイする

### 3.7 Infrastructure as Code を介してリソースを実装する。 以下のようなタスクを行います。

#### a. Cloud Foundation Toolkit テンプレートを使用してインフラストラクチャを構築し、ベスト プラクティスを実装する
``` shell
gcloud compute images list

# gcloud deployment-manager deployments create
gcloud deployment-manager deployments create quickstart-deployment --config vm.yaml
# gcloud deployment-manager deployments describe
gloud deployment-manager deployments describe quickstart-deployment

```
#### b. Google Kubernetes Engine に Config Connector をインストールして構成し、リソースの作成、更新、削除、保護に利用する



## セクション 4. クラウド ソリューションの正常な運用

### 4.1 Compute Engine リソースを管理する。次のようなタスクがあります。

#### a. 単一の VM インスタンスを管理する（起動、停止、構成の編集、インスタンスの削除など）
#### b. インスタンスへリモート接続する
#### c. GPU を新しいインスタンスに接続し、必要な依存関係をインストールする
#### d. 現在実行されている VM のインベントリ（インスタンス ID、詳細）を見る
#### e. スナップショットを操作する（例: VM からのスナップショットの作成、スナップショットの表示、スナップショットの削除など）
#### f. イメージを操作する（例: VM またはスナップショットからのイメージの作成、イメージの表示、イメージの削除など）
#### g. インスタンス グループを操作する（例: 自動スケーリング パラメータの設定、インスタンス テンプレートの割り当てや作成、インスタンス グループの削除など）
#### h. 管理インターフェースを操作する（例: Google Cloud コンソール、Cloud Shell、Cloud SDK など）

### 4.2 Google Kubernetes Engine リソースを管理する。以下のようなタスクを行います。

#### a. 現在実行されているクラスタのインベントリ（ノード、Pod、サービス）を見る
#### b. Docker イメージを参照し、その詳細を Artifact Registry で確認する
#### c. ノードプールを操作する（例: ノードプールの追加、編集、削除など）
#### d. Pod を操作する（例: Pod の追加、編集、削除）
#### e. サービスを操作する（例: サービスの追加、編集、削除）
#### f. ステートフル アプリケーション（例: 永続ボリューム、ステートフル セット）を操作する
#### g. 水平自動スケーリングと垂直自動スケーリングの構成を管理する
#### h. 管理インターフェースを操作する（例: Google Cloud コンソール、Cloud Shell、Cloud SDK、kubectl など）

### 4.3 Cloud Run リソースを管理する。以下のようなタスクを行います。

#### a. アプリケーションのトラフィック分割パラメータを調整する
#### b. 自動スケーリング インスタンスのスケーリング パラメータを設定する
#### c. Cloud Run（フルマネージド）と Cloud Run for Anthos のどちらを実行するかを決定する

### 4.4 ストレージとデータベースのソリューションを管理する。以下のようなタスクを行います。

#### a. Cloud Storage のバケット内またはバケット間でオブジェクトを管理、保護する
#### b. Cloud Storage バケットのオブジェクト ライフサイクル管理ポリシーを設定する
#### c. データ インスタンス（例: Cloud SQL、BigQuery、Cloud Spanner、Cloud Datastore、Cloud Bigtable など）からデータを取得するクエリを実行する
#### d. データ ストレージ リソースのコストを見積もる
#### e. データ インスタンス（例: Cloud SQL、Datastore など）のバックアップと復元を行う
#### f. Dataproc、Dataflow、BigQuery のジョブ ステータスを確認する

### 4.5 ネットワーキング リソースを管理する。以下のようなタスクを行います。

#### a. 既存の VPC にサブネットを追加する
#### b. サブネットを拡張して IP アドレスを増やす
#### c. 静的外部または内部 IP アドレスを予約する
#### d. CloudDNS、CloudNAT、ロードバランサ、ファイアウォール ルールを操作する

### 4.6 モニタリングとロギングを行う。以下のようなタスクを行います。

#### a. リソース指標に基づく Cloud Monitoring アラートの作成
#### b. Cloud Monitoring のカスタム指標（例: アプリケーションやログなどの指標）の作成と取り込み
#### c. ログが外部システムにエクスポートされるようにログシンクを構成する（例: オンプレミスまたは BigQuery など）
#### d. ログルーターを構成する
#### e. Cloud Logging のログを表示、フィルタリングする
#### f. Cloud Logging で特定のログメッセージの詳細を見る
#### g. Cloud Diagnostics を使用してアプリケーションの問題を調査する（例: Cloud Trace データの表示、Cloud Debug を使用したアプリケーションのポイントインタイムの表示など）
#### h. Google Cloud のステータスを確認する

## セクション 5. アクセスとセキュリティの構成

### 5.1 Identity and Access Management（IAM）を管理する。 以下のようなタスクを行います。

#### a. IAM ポリシーの表示

``` shell
# プロジェクトに紐づくポリシーの表示(プロジェクト内のbindingsを表示)
gcloud projects get-iam-policy ace-exam-project

# roleの詳細を表示するコマンド
gcloud iam roles describe roles/appengine.deployer
```
#### b. IAM ポリシーの作成

``` shell
# roleのbinging
# gcloud projects add-iam-policy-binding [RESOURCE-NAME] --member user:[USER- EMAIL] --role [ROLE-ID]
gcloud projects add-iam-policy-binding ace-exam-project --member user:jane@ aceexam.com --role roles/appengine.deployer
```

原則　　
> セキュリティのベストプラクティスは、最小限の権限を割り当てることと、職務の分離を維持することの2つです。
> 最小特権の原則とは、ユーザーやサービス・アカウントが必要なタスクを実行するために必要な最小限の権限セットのみを付与することです。
> 例えば、ユーザーがデータベースの読み取り権限だけで必要なことをすべて実行できるのであれば、書き込み権限を与えるべきではない。

#### c. さまざまなロールタイプの管理とカスタム IAM ロールの定義（基本ロール、事前定義ロール、カスタムロールなど）　

注意
> Google により管理されて適宜更新される事前定義ロールとは異なり、カスタムロールは、新しい権限が使用可能になったときに組織により保守されます。
> つまり、カスタムロールを作ると自分達で権限の管理や更新対応をしないといけなくなる。今回はtableの最終更新日だけでいい（viewは不要）と思います。

[gcloud iam roles create](https://cloud.google.com/sdk/gcloud/reference/iam/roles/create)

``` shell
# カスタムロールの作り方（stageをALPHA, BETA, GA, DEPRECATED, DISABLED, EAPから選択する必要がある）
# gcloud iam roles create [ROLE-ID] \
# --project [PROJECT-ID] --title [ROLE-TITLE] \
# --description [ROLE-DESCRIPTION] --permissions [PERMISSIONS-LIST] --stage [LAUNCH-STAGE]
gcloud iam roles create customAppEngine1 \
--project ace-exam-project --title='Custom Update App Engine' \
--description='Custom update' --permissions=appengine.applications.update --stage=alpha
```

### 5.2 サービス アカウントを管理する。以下のようなタスクを行います。

#### a. サービス アカウントの作成
#### b. 最小限の権限を持つ IAM ポリシー内のサービス アカウントの使用
#### c. サービス アカウントのリソースへの割り当て
#### d. サービス アカウントの IAM の管理
#### e. サービス アカウントの権限借用の管理
#### f. 有効期間が短いサービス アカウント認証情報の作成と管理

### 5.3 監査ログの表示
