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

- (extreme-persistent-disk)[https://www.topgate.co.jp/gce-2021-information#extreme-persistent-disk]
- (永続ディスクの概要)[https://cloud.google.com/compute/docs/disks/persistent-disks?hl=ja]
- (開発者のためストレージ選択の指針！Googleが指南するGCPでの最適なストレージの選び方)[https://www.topgate.co.jp/google-cloud-day-storage]

##### gce
gceのディスクについて
- 標準 Persistent Disk
- バランス Persistent Disk
- SSD Persistent Disk
- extreme-persistent-disk

gceのディスクを決める際には、下記2点を考えないといけないです。
- サイズ
- インタフェース(NVMe と SCSI)

##### gcs
- Standard
  頻繁にアクセスするデータを保管するのに適しています。例えばCDNのようにコンテンツをグローバルに提供したり、ビデオのトランスコーディングのようにある程度のスループットが求められるようなデータの保管に利用することができます。
- Nearline
  目安として月に1度程度のアクセスがある場合に適しています。例えばバックアップデータ等があげられます。
- Coldline
  1年に1回程度しかアクセスしない、ほとんど触らないデータを保存するようなケースに適しています。画像のアーカイブや災害対策用のデータ等が該当します。
- Archive
  長期保存が必要なデータを保管するのに適しています。監査やコンプライアンス上の対応によりデータを長期的に保管しておく必要がある場合や、テープで保管していたデータの置き換えなどに利用することが想定されます。

##### ストレージの選択基準
- Read and Write Patterns
- Consistency
- Transaction Support
- Cost
- Latency

### 2.4 ネットワーク リソースを計画し、構成する。以下のようなタスクを行います。

#### a. 負荷分散オプションの違いを見分ける

[Google Cloud （GCP）の多彩なロードバランサーを一挙に紹介！](https://www.topgate.co.jp/google-cloud-load-balancer)

- L7(HTTP, HTTPS, WebSocket)
    - 外部HTTP（S）負荷分散(マルチリージョンバックエンド)
    - 内部HTTP（S）負荷分散(シングルリージョンバックエンド)
- L4(TCP, UDP)
    - TCP/SSLプロキシ負荷分散(外部)(マルチリージョンバックエンド)
    - 外部 TCP/UDPネットワーク負荷分散(パススルー)(シングルリージョンバックエンド)
    - 内部 TCP/UDP負荷分散(パススルー)(シングルリージョンバックエンド)

#### b. 可用性を考慮してネットワーク内のリソースのロケーションを決定する
#### c. Cloud DNS の構成

- Aレコードは、IPv4でホスト名をIPアドレスにマッピングします。
- AAAAレコードは、IPv6で名前をIPv6アドレスにマップするために使用されます。
- CNAMEレコードは、ドメインの別名が含まれる正規の名前を保持します。
- NSレコードとSOAレコードが追加される。
- NSはネームサーバーレコードであり、ゾーン情報を管理する権威サーバーの アドレスを持つ。
- SOAは権威の開始レコードで、ゾーンに関する権威的な情報を持っています。
- AレコードやCNAMEレコードなど、他のレコードを追加することもできます。
- TTL (time to live)およびTTL Unitパラメータは、レコードがキャッシュに保存される期間を指定します。

``` shell
gcloud beta dns managed-zones create ace-exam-zone1 --description= --dns- name=aceexamzone.com.
gcloud beta dns managed-zones create ace-exam-zone1 --description= --dns- name=aceexamzone.com. --visibility=private --networks=default

# To add an A record
gcloud dns record-sets transaction start --zone=ace-exam-zone1
gcloud dns record-sets transaction add 192.0.2.91 --name=aceexamzone.com. --ttl=300 --type=A --zone=ace-exam-zone1
gcloud dns record-sets transaction execute --zone=ace-exam-zone1.

# To create a CNAME record
gcloud dns record-sets transaction start --zone=ace-exam-zone1
gcloud dns record-sets transaction add server1.aceexamezone.com. -- name=www2.aceexamzone.com. --ttl=300 --type=CNAME --zone=ace-exam-zone1
gcloud dns record-sets transaction execute --zone=ace-exam-zone1
```



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

注意：Cloud Functionsは世代で動きが違うので確認
- (Cloud Functionsの世代比較)[https://cloud.google.com/functions/docs/concepts/version-comparison]

cloud functionsの場合
[deploy例](https://cloud.google.com/functions/docs/deploy#cli-examples)
``` bash
gcloud functions deploy my-http-function \
  --gen2 \
  --region=us-central1 \
  --runtime=nodejs16 \
  --source=. \
  --entry-point=myHttpFunction \
  --trigger-http
```

cloud runの場合
``` bash
```

#### b. Google Cloud イベント（Pub/Sub イベント、Cloud Storage オブジェクト変更通知イベントなど）を受け取るアプリケーションをデプロイする

##### トリガー
- HTTP トリガー
- イベント トリガー:
  - Pub/Sub トリガー
    - オブジェクトのファイナライズ(新しいオブジェクトが作成されるか、既存のオブジェクトが上書きされ、そのオブジェクトの新しい世代が作成されると送信されます。)
      - 第 2 世代: google.cloud.storage.object.v1.finalized（Eventarc 経由）
      - 第 1 世代: google.storage.object.finalize
    - オブジェクトの削除(オブジェクトが完全に削除された場合に発生します。)
      - 第 2 世代: google.cloud.storage.object.v1.deleted（Eventarc 経由）
      - 第 1 世代: google.storage.object.delete
    - オブジェクトのアーカイブ(オブジェクトのライブ バージョンが非現行バージョンになると送信されます。詳細については、オブジェクトのバージョニングをご覧ください。)
      - 第 2 世代: google.cloud.storage.object.v1.archived（Eventarc 経由）
      - 第 1 世代: google.storage.object.archive
    - オブジェクト メタデータの更新(既存オブジェクトのメタデータが変更された場合に送信されます。)
      - 第 2 世代: google.cloud.storage.object.v1.metadataUpdated（Eventarc 経由）
      - 第 1 世代: google.storage.object.metadataUpdate
  - Cloud Storage トリガー
  - Firestore トリガー
    - Firebase 向け Google アナリティクス トリガー
    - Firebase Realtime Database トリガー
    - Firebase Authentication トリガー
    - Firebase Remote Config トリガー

cloud functionsの場合
[deploy例](https://cloud.google.com/functions/docs/deploy#cli-examples)
``` bash
gcloud functions deploy my-pubsub-function \
  --gen2 \
  --region=europe-west1 \
  --runtime=python39 \
  --source=gs://my-bucket/my_function_source.zip \
  --entry-point=pubsub_handler \
  --trigger-topic=my-topic

gcloud functions deploy my-storage-function \
  --gen2 \
  --region=asia-northeast1 \
  --runtime=java11 \
  --source=./functions/storage-function \
  --entry-point=myproject.StorageFunction \
  --trigger-event-filters="type=google.cloud.storage.object.v1.deleted" \
  --trigger-event-filters="bucket=my-bucket"

# 実行する場合はurlを叩くか、下記コマンドを利用
gcloud functions describe nodejs-http-function --gen2 --region REGION --format="value(serviceConfig.uri)"
```

### 3.4 データ ソリューションをデプロイし、実装する。以下のようなタスクを行います。

#### a. 製品を使用してデータシステムを初期化する（例:Cloud SQL、Firestore、BigQuery、Cloud Spanner、Pub/Sub、Cloud Bigtable、Dataproc、Dataflow、Cloud Storage など）

pub/sub
```shell
# gcloud pubsub topics create [TOPIC_NAME]
# gcloud pubsub subscriptions create --topic [TOPIC_NAME] [SUBSCRIPTION_NAME]
gcloud pubsub topics create ace-exam-topic1
gcloud pubsub subscriptions create --topic=ace-exam-topic1 ace-exam-sub1

# gcloud pubsub topics publish [TOPIC_NAME] --message [MESSAGE]
# gcloud pubsub subscriptions pull --auto-ack [SUBSCRIPTION_NAME]
gcloud pubsub topics publish ace-exam-topic1 ––message "first ace exam message" gcloud pubsub subscriptions pull ––auto-ack ace-exam-sub1

```

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

``` shell
gcloud compute target-pools create ace-exam-pool
gcloud compute forwarding-rules create ace-exam-lb --port=80 --target-pool ace-exam-pool
gcloud compute target-pools add-instances ace-exam-pool --instances ig1,ig2
```


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
GCS
``` shell
# ストレージクラスを変更したい場合
gsutil rewrite -s [STORAGE_CLASS] gs://[PATH_TO_OBJECT]

# オブジェクトの移動をする場合
gsutil mv gs://[SOURCE_BUCKET_NAME]/[SOURCE_OBJECT_NAME] \
gs://[DESTINATION_BUCKET_NAME]/[DESTINATION_OBJECT_NAME]

# オブジェクトのリネームをしたい場合
gsutil mv gs://[BUCKET_NAME]/[OLD_OBJECT_NAME] gs://[BUCKET_NAME]/[NEW_OBJECT_NAME]
```

#### b. Cloud Storage バケットのオブジェクト ライフサイクル管理ポリシーを設定する

```shell
# <make bucket> gsutil mb gs://[BUCKET_NAME]/
gsutil mb gs://ace-exam-bucket1/

# gsutil cp [LOCAL_OBJECT_LOCATION] gs://[DESTINATION_BUCKET_NAME]/
gsutil cp /home/mydir/README.txt gs://ace-exam-bucket1/
gsutil cp gs://ace-exam-bucket1/README.txt /home/mydir/

gsutil mv \
 gs://[SOURCE_BUCKET_NAME]/[SOURCE_OBJECT_NAME] \
 gs://[DESTINATION_BUCKET_NAME]/[DESTINATION_OBJECT_NAME]
gsutil mv gs://ace-exam-bucket1/README.txt gs://ace-exam-bucket2/
```

```shell
gsutil lifecycle set LIFECYCLE_CONFIG_FILE gs://BUCKET_NAME
gsutil lifecycle get gs://BUCKET_NAME
```

LIFECYCLE_CONFIG_FILEの例
[設定ファイルの詳細](https://cloud.google.com/storage/docs/json_api/v1/buckets?hl=ja#resource-representations)

- オブジェクトの経過時間が 365 日（1 年）を超えていて、かつ、現在のストレージ クラスが
  Standard ストレージ、Multi-Regional ストレージ、Durable Reduced Availability（DRA）ストレージのいずれかの場合、
  オブジェクトのストレージ クラスを Nearline ストレージに変更する。
- オブジェクトの経過期間が 1,095 日（3 年）を超えていて、かつ、現在のストレージ クラスが
  Nearline ストレージの場合、オブジェクトのストレージ クラスを Coldline ストレージに変更する。
``` json
{
    "lifecycle": {
        "rule": [
            {
                "action": {
                    "type": "SetStorageClass",
                    "storageClass": "NEARLINE" // conditionのものをNearlineへ移動させる
                },
                "condition": {
                    "age": 365,
                    "matchesStorageClass": ["MULTI_REGIONAL", "STANDARD", "DURABLE_REDUCED_AVAILABILITY"]
                }
            },
            {
                "action": {
                    "type": "SetStorageClass",
                    "storageClass": "COLDLINE" // conditionのものをColdlineへ移動させる
                },
                "condition": {
                    "age": 1095,
                    "matchesStorageClass": ["NEARLINE"]
                }
            }
        ]
    }
}
```


#### c. データ インスタンス（例: Cloud SQL、BigQuery、Cloud Spanner、Cloud Datastore、Cloud Bigtable など）からデータを取得するクエリを実行する

Cloud SQL
``` shell
# 接続からsqlの利用まで
gcloud sql connect ace-exam-mysql –user=root
CREATE DATABASE ace_exam_book;
USE ace_exam_book;
CREATE TABLE books (title VARCHAR(255), num_chapters INT, entity_id INT NOT NULL \AUTO_INCREMENT, PRIMARY KEY (entity_id));INSERT INTO books (title,num_chapters) VALUES ('ACE Exam Study Guide', 18);
INSERT INTO books (title,num_chapters) VALUES ('Architecture Exam Study Guide', 18);
SELECT * from books;
```
Cloud Datastore
　- GQLを利用する

BigQuery
``` shell
# bq ––location=[LOCATION] query ––use_legacy_sql=false ––dry_run [SQL_QUERY]

# クエリ実行のjobを確認するコマンド
bq --location=US show -j gcpace-project:US.bquijob_119adae7_167c373d5c3
```

Cloud Spanner
``` shell
```

Cloud Bigtable
``` shell
gcloud components update
gcloud components install cbt
echo instance = ace-exam-bigtable >> ~/.cbtrc

cbt createtable ace-exam-bt-table
cbt ls
# カラムを作るためにfamilyを作成
cbt createfamily ace-exam-bt-table colfam1
# familyにカラムを追加
cbt set ace-exam-bt-table row1 colfam1:col1=ace-exam-value
cbt read ace-exam-bt-table
```

Dataproc
``` shell
gcloud dataproc clusters create cluster-bc3d ––zone us-west2-a
gcloud dataproc jobs submit spark ––cluster cluster-bc3d ––jar ace_exam_jar.jar
```

#### d. データ ストレージ リソースのコストを見積もる
#### e. データ インスタンス（例: Cloud SQL、Datastore など）のバックアップと復元を行う

> Cloud SQLの場合
``` shell
# gcloud sql backups create ––async ––instance [INSTANCE_NAME]
gcloud sql backups create ––async ––instance ace-exam-mysql

# gcloud sql instances patch [INSTANCE_NAME] –backup-start-time [HH:MM]
gcloud sql instances patch ace-exam-mysql –backup-start-time 01:00
```

``` shell
# バックアップの入れ先の作成
gsutil mb gs://ace-exam-bucket1/
# gcloud sql instances describe [INSTANCE_NAME]
gcloud sql instances describe ace-exam-mysql1

# gsutil acl ch -u [SERVICE_ACCOUNT_ADDRESS]:W gs://[BUCKET_NAME]
gsutil acl ch -u tnkknzut25bezoq72bjbfmo5hu@spe-umbra-30.iam.gserviceaccount.com /
 :W gs://ace-exam-bucket1

# gcloud sql export sql [INSTANCE_NAME] gs://[BUCKET_NAME]/[FILE_NAME] \ --database=[DATABASE_NAME]
gcloud sql export sql ace-exam-mysql1 \
 gs://ace-exam-buckete1/ace-exam-mysqlexport.sql \
　　--database=mysql

# csvでのバックアップをする場合
gcloud sql export csv ace-exam-mysql1 gs://ace-exam-buckete1/ace-exam-mysql-export.csv \ --database=mysql

# gcloud sql import sql [INSTANCE_NAME] gs://[BUCKET_NAME]/[IMPORT_FILE_NAME] \ --database=[DATABASE_NAME]
gcloud sql import sql ace-exam-mysql1 gs://ace-exam-buckete1/ace-exam-mysql-export.sql \ --database=mysql
```

> Datastoreの場合
- バックアップ：GCSにバックアップファイルをアップロード
- バックアップの利用：GCSからバックアップファイルをインポート
``` shell
# gsutil mb gs://[BUCKET_NAME]/
gsutil mb gs://ace_exam_backups/

# gcloud –namespaces='[NAMESPACE]' gs://[BUCKET_NAME}
gcloud datastore export –namespaces='(default)' gs://ace_exam_backups

# gcloud datastore import gs://[BUCKET]/[PATH]/[FILE].overall_export_metadata
gcloud datastore import gs://ace_exam_backups/[FILE].overall_export_metadata
```


``` shell
# gcloud datastore export --namespaces="(default)" gs://${BUCKET}
gcloud datastore export \
 --namespaces="(default)" \
 gs://ace-exam-datastore1

# gcloud datastore import gs://${BUCKET}/[PATH]/[FILE].overall_export_metadata
gcloud datastore import \
 gs://ace-exam-datastore1/2018-12-20T19:13:55_64324/ \
 2018-12-20T19:13:55_64324.overall_export_metadata
```

BigQueryの場合
```shell
# bq extract --destination_format [FORMAT] --compression [COMPRESSION_TYPE] --field_delimiter [DELIMITER] --print_header [BOOLEAN] [PROJECT_ID]:[DATASET]. [TABLE] gs://[BUCKET]/[FILENAME]
bq extract \
 --destination_format CSV \
 --compression GZIP \
 'mydataset.mytable' \
 gs://example-bucket/myfile.zip

# bq load --autodetect --source_format=[FORMAT] [DATASET].[TABLE] [PATH_TO_SOURCE]
# autodetect パラメータは、bq load がソースファイルからテーブルスキーマを自動的に検出するようにします。
bq load \
 --autodetect \
 --source_format=CSV \
 mydataset.mytable \
 gs://ace-exam-biquery/mydata.csv
```

Cloud Spanner
[Cloud Spanner がポイントインタイム リカバリ機能への対応を開始](https://cloud.google.com/blog/ja/products/databases/continuous-data-protection-here-for-spanner-with-pitr)

backupと復元方法
- Cloud console
- CLI
- クライアントライブラリ

```shell
# backup
gcloud spanner backups create example-db-backup-6 \
 --instance=test-instance \
 --database=example-db \
 --retention-period=1y \
 --async

# restore
gcloud spanner databases restore \
 --async \
 --destination-instance=test-instance \
 --destination-database=example-db-restored \
 --source-instance=test-instance \
 --source-backup=example-db-backup-6
```

Bigtableの場合

backupと復元方法
- Cloud console
- CLI
- クライアントライブラリ


``` shell
# backup
gcloud  bigtable backups create BACKUP_ID \
 --instance=INSTANCE_ID
 --cluster=CLUSTER_ID \
 --table=TABLE_ID \
 --async \
 --expiration-date=EXPIRATION_DATE \
 --retention-period=RETENTION_PERIOD

# resotre
gcloud bigtable instances tables restore
 --source-instance=INSTANCE_ID_SOURCE \
 --source-cluster=CLUSTER_ID \
 --source=BACKUP_ID \
 --destination-instance=INSTANCE_ID_DESTINATION \
 --destination=NEW_TABLE_ID \
 --async
```

Dataprocの場合
``` shell
gcloud components install beta


# gcloud beta dataproc clusters export [CLUSTER_NAME] --destination=[PATH_TO_EXPORT_FILE]
gcloud beta dataproc clusters export ace-exam-dataproc-cluster \
 --destination=gs://ace-exam-bucket1/mydataproc.yaml

# gcloud beta dataproc clusters import [SOURCE_FILE]
gcloud beta dataproc clusters import gs://ace-exam-bucket1/mydataproc.yaml
```

#### f. Dataproc、Dataflow、BigQuery のジョブ ステータスを確認する

### 4.5 ネットワーキング リソースを管理する。以下のようなタスクを行います。

#### a. 既存の VPC にサブネットを追加する
#### b. サブネットを拡張して IP アドレスを増やす

``` shell
gcloud compute networks subnets expand-ip-range ace-exam-subnet1 --prefix-length 16
```


- 静的IPアドレスは、プロジェクトに割り当てられ、リリースされるまで使用されます。Webサイトなどのサーバーに固定IPアドレスが必要な場合に使用されます。
- エフェメラルIPアドレスは、リソースがIPアドレスを使用している間のみ存在し、例えば、同じプロジェクト内の他のVMからのみアクセスされるアプリケーションを実行しているVM上に存在します。

#### c. 静的外部または内部 IP アドレスを予約する

`--network-tier`には、2つのサービスがあります。
- 低コストのStandardサービス層を使用するオプションがあり、これは一部のデータ転送にインターネットを使用します。
- Premiumは、Googleのグローバルネットワークを介してすべてのトラフィックをルーティングします。

``` shell
gcloud beta compute addresses create ace-exam-reserved-static1 --region=us-west2 --network-tier=PREMIUM
```

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


## 6。その他
### 6.1 memory store
nodeのサイズについて
- redis: 1-300GB
- MEMCACHED: 1- 256GB

### 6.2 app engine

####　app engineの階層構造について

- Application
  - service
  - service
  - service
    - version
    - version
      - instance

app engineのデフォルトURLは、
> https://[プロジェクトid].appspot.com/

``` shell
gcloud components install app-engine-python

# デプロイ(app.yaml, main.py, main_test.pyがある想定)
# 　よく使うオプジョン
# --version to specify a custom version ID
# --project to specify the project ID to use for this app 
# --no-promote to deploy the app without routing traffic to it
gcloud app deploy app.yml
```

#### app.ymlについて

- [スケーリング設定について](https://cloud.google.com/appengine/docs/standard/python3/config/appref?hl=ja#scaling_elements)

#### トラフィック分割について

分割手法
- IP address
- HTTP cookie(`GOOGAPPUID`を利用)
- random selection


``` shell
# トラフィックの分割について
# gcloud app services set-traffic [MY_SERVICE] \
#  --splits [MY_VERSION1]=[VERSION1_WEIGHT],[MY_VERSION2]=[VERSION2_WEIGHT]
#  --split-by [IP_OR_COOKIE]
gcloud app services set-traffic serv1 --splits v1=.4,v2=.6

gcloud app services set-traffic [MY_SERVICE] --splits [MY_VERSION]=1 --migrate
```
