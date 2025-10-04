# SNS & SQS

概要

- SNS は通知サービス、SQS はキューサービス。組み合わせて非同期処理やイベント駆動を実現する。

重要ポイント

- SNS トピックとサブスクリプション
- SQS キュー（標準、FIFO）
- Dead-letter queue (DLQ)
- メッセージの順序管理と重複排除

学習タスク

- SNS と SQS を連携してメッセージングフローを作成

練習問題

1. 高スループットのイベント処理を設計する場合のキュー選択と構成は？

参考リンク

- https://aws.amazon.com/sns/
- https://aws.amazon.com/sqs/
