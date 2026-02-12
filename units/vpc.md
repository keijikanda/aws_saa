# VPC

概要

- Amazon Virtual Private Cloud (VPC) は AWS 上の仮想ネットワークを提供するサービス。
重要ポイント

- サブネット (パブリック、プライベート、データベース用)
学習タスク

- VPC を設計して、パブリック/プライベートサブネットを作成する
練習問題

1. ハイブリッド構成でオンプレミスと VPC を接続する方法を列挙し、それぞれのメリット・デメリットを説明してください。

参考リンク

- https://aws.amazon.com/vpc/
# VPC

概要

- Amazon Virtual Private Cloud (VPC) は AWS 上の仮想ネットワークを提供するサービスで、サブネット、ルート、ゲートウェイ、ACL、セキュリティグループを組み合わせてネットワークを設計します。

## 主要コンポーネント

- サブネット: AZ ごとに作成し、パブリック（IGW 経由でインターネットアクセス可能）とプライベート（NAT 経由でアウトバウンドのみ）に分けるのが一般的。
- ルートテーブル: サブネットに関連づけてトラフィックの送信先を定義。
- インターネットゲートウェイ (IGW): VPC とインターネットを接続するゲートウェイ（パブリックサブネットに必要）。
- NAT Gateway / NAT インスタンス: プライベートサブネットからのアウトバウンド接続を可能にする。NAT Gateway はマネージドで高可用だがコストがかかる。

### NAT Gateway / NAT インスタンス（詳細）

- **配置と接続**: NAT Gateway は通常 **パブリックサブネット** に作成します。パブリックサブネットに配置された NAT Gateway には Elastic IP が割り当てられ、インターネットゲートウェイ(IGW) を経由してインターネットへアクセスします。プライベートサブネット側では、ルートテーブルに `0.0.0.0/0` のデフォルトルートを NAT Gateway の ID（`nat-xxxxxxxx`）に向けることで、インスタンスのアウトバウンド通信が NAT Gateway 経由になります。

- **双方向性の注意**: NAT Gateway はプライベートサブネット内のインスタンスからのアウトバウンドを許可しますが、インターネット側から NAT 経由でプライベートインスタンスへ直接到達することはできません（インバウンド接続は許可されない）。外部からの受信を必要とする場合は、パブリックサブネット上の ALB/ELB や Bastion ホスト等を使います。

- **高可用化（AZ 単位）**: NAT Gateway はサブネット（＝AZ）ごとに作成されます。AZ 障害を避けるため、各 AZ に NAT Gateway を配置し、各 AZ のプライベートサブネットが同一 AZ の NAT Gateway を使うようルートを設定するのが推奨です（クロス AZ の通信はデータ転送費用が発生するため）。

- **コストと運用**: NAT Gateway はマネージドで運用負荷が低く、スケーリングやパッチングの心配がありませんが、時間当たりの利用料と処理データ量に基づく課金が発生します。小規模でコスト重視の場合は自前の NAT インスタンス（EC2 を NAT として設定）を使う選択肢がありますが、可用性・スケーリング・運用コストが増えます。NAT インスタンスでは `source/destionation check` を無効化する必要があります。

- **CLI での作成（例）**:

```bash
# Elastic IP を確保
aws ec2 allocate-address --domain vpc

# NAT Gateway 作成（subnet-id と allocation-id を指定）
aws ec2 create-nat-gateway --subnet-id subnet-01234567 --allocation-id eipalloc-01234567
```

- **代替手段**: S3 等へのアクセスであれば VPC Gateway Endpoint（S3 用）を使うことで NAT を経由せずにプライベートに通信でき、データ転送コストを削減できます。

- VPC エンドポイント (Interface / Gateway): S3, DynamoDB 等へのプライベート接続を提供し、インターネットを経由せずにアクセス可能にする。
- セキュリティグループ / NACL: セキュリティグループはインスタンスレベルのステートフルな制御、NACL はサブネットレベルのステートレスなフィルタ。

## 接続パターン

- VPC ピアリング: 同一または別アカウントの VPC を直接接続。トランジティブルーティングはサポートしない。
- Transit Gateway (TGW): 多数の VPC やオンプレミス接続を集中管理するハブ。トランジティブルーティングをサポート。
- VPN (Site-to-Site): オンプレミスと VPC を IPSec VPN で接続。冗長化や帯域幅の考慮が必要。
- Direct Connect: 専用回線で低レイテンシ / 高帯域を実現。冗長化やローカルリージョンとの接続設計がポイント。

## 高可用性設計の考慮

- サブネットは AZ ごとに作る（最低 2 AZ 推奨）。
- NAT Gateway は AZ 毎に配置し、ルートテーブルで冗長化。
- ELB はマルチ AZ 対応で、ターゲットは複数 AZ のインスタンスを含める。

## セキュリティと監査

- VPC Flow Logs: ネットワークトラフィックのログを CloudWatch Logs や S3 に記録して監査やトラブルシュートに利用。
- セキュリティグループは最小権限で設定し、管理用の踏み台ホストは限定したアクセスで運用。

## コストと運用

- NAT Gateway はデータ転送料金と時間当たりの課金が発生する。小規模では NAT インスタンスの方が安価な場合があるが、管理と可用性は注意。
- VPC エンドポイントを使うことで NAT / IGW を経由するトラフィックを削減でき、コスト効果が出るケースがある（特に S3 への大量通信）。

## CLI / IaC の例

```bash
# VPC 作成（例）
aws ec2 create-vpc --cidr-block 10.0.0.0/16

# サブネット作成（例）
aws ec2 create-subnet --vpc-id vpc-01234567 --cidr-block 10.0.1.0/24 --availability-zone ap-northeast-1a

# VPC Flow Logs の作成（CloudWatch Logs へ）
aws ec2 create-flow-logs --resource-type VPC --resource-ids vpc-01234567 --traffic-type ALL --log-group-name /aws/vpc/flowlogs --deliver-logs-permission-arn arn:aws:iam::123456789012:role/flow-logs-role
```

## 試験向けポイント / よく出るトピック

- VPC ピアリングはトランジティブではない（A↔B, B↔C があっても A↔C は通信できない）。
- Transit Gateway は多数の VPC とオンプレ接続を中央集約するのに向く。
- VPC エンドポイント (Gateway) は S3 と DynamoDB 用、Interface Endpoint は他多数の AWS サービス用。
- セキュリティグループはステートフル、NACL はステートレス。

## サンプル問題

1. オンプレミスと複数 VPC を接続したい。規模が大きく将来増える可能性がある場合、VPC ピアリングと Transit Gateway を比較し、どちらを選ぶか理由とコスト面での考慮点を述べてください。

2. プライベートサブネットのインスタンスが S3 にアクセスする際にインターネットを経由しない方法を 2 つ挙げてください（期待値: NAT Gateway と VPC Gateway Endpoint の違いと推奨ケース）。

参考リンク

- https://aws.amazon.com/vpc/
