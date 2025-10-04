# ELB & Auto Scaling

概要

- Elastic Load Balancing (ELB) はトラフィックを分散して可用性を高めるサービスで、Auto Scaling は負荷に応じてインスタンス数を自動調整します。

## LB の種類と違い

- Application Load Balancer (ALB): HTTP/HTTPS レイヤーでのルーティング（パスベース、ホストベース、WebSocket 対応）。ターゲットは IP/インスタンス/コンテナ。
- Network Load Balancer (NLB): 高パフォーマンスで低レイテンシ、TCP/UDP レベルで動作。静的 IP（Elastic IP）をサポート。
- Classic Load Balancer (CLB): レガシー。L4/L7 の基本機能を提供するが新機能は ALB/NLB に移行推奨。

## ターゲットグループとヘルスチェック

- ターゲットグループにターゲットを登録し、ヘルスチェックで健全性を確認。ヘルスチェックの閾値やタイムアウトは重要なチューニングポイント。

## Auto Scaling Group のポリシー

- スケーリングポリシー: ターゲット追跡（指定したメトリクスを維持）、ステップスケーリング（しきい値に応じた増減）、スケジュールベース。
- ライフサイクルフック: インスタンス起動/終了時にカスタム処理（設定やログの取得など）を差し込める。

## デプロイ戦略

- Rolling Update, Rolling with Batch, Blue/Green（CodeDeploy と組み合わせ）など。ダウンタイム最小化のための戦略を理解する。

## 設計上の考慮点

- ステートフルアプリのスケーリング課題: セッション管理（Sticky Session の制約）、状態同期、データ一貫性。
- ヘルスチェックの誤検知によるスケールイン/アウトの不安定化を防ぐための調整。

## CLI / IaC の例

```bash
# ASG を作成して ALB と紐づけるなどは CloudFormation / CDK で定義するケースが多い
aws autoscaling create-auto-scaling-group --auto-scaling-group-name my-asg --launch-configuration-name my-lc --min-size 2 --max-size 10 --vpc-zone-identifier "subnet-0123,subnet-0456"
```

## 試験向けポイント / よく出るトピック

- ALB はレイヤー7 ルーティングをサポートし、NLB は高スループットと静的 IP を必要とするケースに使う点。
- スケーリングポリシーのタイプ（ターゲット追跡 vs ステップ vs スケジュール）とユースケース。

## サンプル問題

1. セッション情報を持つアプリケーションをオートスケールする場合、どのようにセッションを扱いますか？（Sticky Session の利点と欠点、代替案）

2. ALB のヘルスチェックが頻繁に失敗する場合の原因と対処法を列挙してください。

参考リンク

- https://aws.amazon.com/elasticloadbalancing/
