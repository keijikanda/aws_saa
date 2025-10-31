# SNS & SQS

概要

- SNS は Pub/Sub 型の通知サービス、SQS はメッセージキューサービス。組み合わせて柔軟な非同期アーキテクチャを構築できます。

## SNS の特徴

- トピックに対して複数のサブスクリプション（HTTP/S, Email, Lambda, SQS, SMS）を設定できる「fan-out」構成が得意。
- フィルタポリシーでメッセージのルーティングを絞り込める。

## SQS の特徴

- 標準キュー: 高スループット・最小一度以上配信（at-least-once）。順序保証なし。
- FIFO キュー: 厳密な順序と exactly-once（重複排除）を提供（スループット上限有り）。
- Visibility Timeout: 消費中のメッセージを他のコンシューマから隠す時間。処理時間に合わせて調整する。
- Long Polling: 空のポーリングの無駄な API 呼び出しを減らす。
- Dead-letter Queue (DLQ): 処理失敗したメッセージの隔離先。

## 設計パターン

- Fan-out: SNS -> SQS（複数のワーカーで並列処理）
- Work Queue: SQS 単体での負荷平準化とフェイルオーバー
- Delay Queue: メッセージの遅延配信

## スケーリングと運用

- SQS は複数のコンシューマによる水平スケールが可能。コンシューマはメッセージの冪等性を保証する。
- メッセージサイズ制限とバッチ処理（ReceiveMessage, DeleteMessageBatch）を利用して効率化。

## CLI / IaC の例

```bash
# SQS キューの作成（FIFO）
aws sqs create-queue --queue-name my-queue.fifo --attributes FifoQueue=true,ContentBasedDeduplication=true

# SNS トピックの作成と SQS へのサブスクライブ
aws sns create-topic --name my-topic
aws sns subscribe --topic-arn arn:aws:sns:ap-northeast-1:123456789012:my-topic --protocol sqs --notification-endpoint arn:aws:sqs:ap-northeast-1:123456789012:my-queue
```

## 試験向けポイント / よく出るトピック

- FIFO と標準キューの違い（順序性と重複排除、スループット特性）。
- Visibility Timeout の挙動と DLQ の使い方。

## サンプル問題

1. 高スループットなイベント処理フローで、順序保証が不要な場合の構成と理由を説明してください。

参考リンク

- https://aws.amazon.com/sns/
- https://aws.amazon.com/sqs/
