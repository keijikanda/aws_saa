# Route 53

概要

- Amazon Route 53 はスケーラブルな DNS ウェブサービスで、ドメイン登録、DNS ルーティング、ヘルスチェック、トラフィック管理を提供します。

## 主要トピック

### レコードとホストゾーン

- パブリックホストゾーンとプライベートホストゾーン（VPC に対して DNS を提供）。
- レコードタイプ: A, AAAA, CNAME, MX, TXT, NS, SRV, Alias など。
- Alias レコードは AWS リソース（ELB, CloudFront, S3 静的サイト）へルーティングでき、追加の DNS クエリコストが発生しない点が利点。

### ルーティングポリシー

- Simple: 単一リソースへのデフォルトルーティング。
- Weighted: トラフィックを重み付けして分配（A/B テスト、段階的デプロイに有用）。
- Latency-based: レイテンシが最小のリージョンへルーティング。
- Failover: プライマリ/セカンダリ構成でヘルスチェックと組み合わせてフェイルオーバーを実装。
- Geolocation / Geoproximity: 地理的なユーザの分配や近接性に基づくルーティング。

### ヘルスチェックとフェイルオーバー

- Route 53 のヘルスチェックでエンドポイントの状態を監視して、異常時に別のエンドポイントへフェイルオーバー可能。
- ヘルスチェックは Endpoint の HTTP ステータスや TCP 接続などで判定。

### Private Hosted Zone と Resolver

- VPC 内専用の DNS 解決が必要な場合はプライベートホストゾーンを使用。
- Route 53 Resolver を使ってオンプレ DNS と AWS VPC DNS を統合する（Forwarding、Resolver Endpoints）。

### ドメイン登録と DNS セキュリティ

- Route 53 でドメイン登録が可能。ドメインの WHOIS 情報、ネームサーバーの管理を行う。
- DNSSEC はドメインとホストゾーンの検証に使える（ドメインレジストリが対応している場合）。

## CLI / IaC の例

```bash
# ホストゾーン作成（例）
aws route53 create-hosted-zone --name example.com --caller-reference "myref-$(date +%s)"

# Alias レコードの作成は change-batch JSON を用いる
```

## 試験向けポイント / よく出るトピック

- Alias レコードと CNAME の違い（Alias はルートドメインで使え、AWS リソースへ直接向けられる）。
- Route 53 のフェイルオーバーとヘルスチェックの組み合わせで高可用性を実現する点。
- Private Hosted Zone と VPC 間の関係と Resolver の用途。

## サンプル問題

1. マルチリージョンでの可用性を向上させるために Route 53 を使った設計を説明してください（レイテンシールーティングやフェイルオーバーを含める）。

参考リンク

- https://aws.amazon.com/route53/
