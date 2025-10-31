# Monitoring & Logging

概要

- 監視とログ管理は運用・障害対応・セキュリティの基盤です。適切に設計されたメトリクスとログの収集・保持・アラートが重要になります。

## ログの中央集約

- CloudWatch Logs を中心にアプリケーションログ、VPC Flow Logs、CloudTrail、ELB アクセスログなどを収集。
- S3 に長期保存して Athena で検索、あるいは OpenSearch / Elasticsearch にインデックスして可視化するパターンもある。
- クロスアカウントでログを中央アカウントへ集約し、セキュリティと監査の管理を集中化する。

## CloudWatch Logs Insights（例）

- ログクエリでエラー率や遅延の傾向を抽出。
- 例: 直近 1 時間で 5xx エラーの割合を求めるクエリ

```text
fields @timestamp, @message
| filter status >= 500
| stats count() as errors by bin(1m)
```

## メトリクス設計とアラート

- 重要なメトリクス（例: CPU, Memory, Disk, ErrorRate, Latency）を選び、ノイズの少ないアラート閾値を設計する。
- Metric Math を使って複合条件（例: 平均 CPU > 80% かつ エラー率 > 1%）を評価してアラートを作る。

## 保持期間とコストのトレードオフ

- ログを長期間保持するとコストが増加するため、保持ポリシーを定義（例: 30 日は CloudWatch、長期は S3）。
- サンプリングやレート制限でログ量を管理する。

## アラート運用（アラート騒音対策）

- しきい値チューニング、サイレンストルール、推移的アラート（一時的なバーストを無視する）を導入。
- Runbook を用意し、アラート発生時の初動手順を定義する。

## CLI / IaC の例

```bash
# CloudWatch Logs Insights のクエリを実行するサンプル
aws logs start-query --log-group-name /aws/lambda/my-function --start-time 1633072800 --end-time 1633076400 --query-string "fields @timestamp, @message | filter @message like /ERROR/ | stats count() by bin(5m)"
```

## 試験向けポイント / よく出るトピック

- CloudWatch Logs Insights の用途と基本的なクエリの考え方。
- 中央集約ログ設計の利点（検索性、セキュリティ監査、コスト最適化）。

## サンプル問題

1. 監視対象のサービスで頻発するアラートを減らすにはどのような手順を踏みますか？（設計・チューニング・運用ルールを含める）

参考リンク

- https://aws.amazon.com/monitoring/
