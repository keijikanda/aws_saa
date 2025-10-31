# CloudWatch & CloudTrail

概要

- CloudWatch はモニタリングと運用自動化のためのプラットフォーム。CloudTrail は AWS API 呼び出しの監査ログを提供します。

## CloudWatch の主要機能

- Metrics: 標準メトリクス（EC2, RDS など）とカスタムメトリクスを収集。
- Alarms: メトリクスに基づくアラートを設定し SNS や Auto Scaling をトリガー可能。
- Logs: CloudWatch Logs にアプリケーションログや VPC Flow Logs を集約。
- Dashboards: 複数メトリクスを可視化するダッシュボードを作成。
- Logs Insights: クエリ言語でログを解析し、リアルタイムで洞察を得る。
- Metric Math: 複数メトリクスの計算・集約をして複合的なアラートを作成可能。

## EventBridge（旧 CloudWatch Events）

- スケジュールや AWS API イベント、カスタムイベントをルーティングして Lambda や Step Functions へ接続。
- EventBridge ルールでイベントパターンに一致する処理を自動実行。

## CloudTrail の活用

- CloudTrail は管理イベント・データイベント・インサイトを記録。
- CloudTrail ログは S3 に配信して長期保存・分析（Athena でクエリ実行）できる。
- 監査・インシデント対応のために CloudTrail と GuardDuty を組み合わせる。

## クロスアカウント / 集約設計

- CloudWatch Logs と CloudTrail を中央アカウントへ集約して監視と監査を一元化するパターン。
- IAM ロールとリソースポリシーで権限を適切に管理。

## 保持期間とコスト管理

- ログの保持期間を適切に設定してコストを管理する（CloudWatch Logs の保管コストは蓄積量に影響）。

## CLI / IaC の例

```bash
# CloudWatch Alarm の作成例
aws cloudwatch put-metric-alarm --alarm-name HighCPU --metric-name CPUUtilization --namespace AWS/EC2 --statistic Average --period 300 --threshold 80 --comparison-operator GreaterThanThreshold --dimensions Name=InstanceId,Value=i-0123456789abcdef0 --evaluation-periods 2 --alarm-actions arn:aws:sns:ap-northeast-1:123456789012:alarm-topic

# CloudTrail の作成と S3 配信はコンソールまたは CloudFormation で管理することが多い
```

## 試験向けポイント / よく出るトピック

- CloudWatch Logs Insights の基本的な利用用途とクエリの目的。
- CloudTrail は API 呼び出しを記録する監査ログであり、リソース操作のトレーサビリティ確保に使う。
- EventBridge はイベントルーティングの中心で、多くのサービス連携のハブになる点。

## サンプル問題

1. ある API コールに対してアラートと調査を自動化したい。CloudTrail, EventBridge, Lambda をどう組み合わせますか？

参考リンク

- https://aws.amazon.com/cloudwatch/
- https://aws.amazon.com/cloudtrail/
