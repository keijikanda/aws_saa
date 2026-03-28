# Lambda & Serverless

概要

- AWS Lambda はイベント駆動のサーバーレスコンピューティングサービスで、インフラ管理不要でコードを実行できます。スケーリング、パッチ適用、インフラの運用は AWS が担当します。

## トリガーと統合

- 主なトリガー: API Gateway, S3, DynamoDB Streams, Kinesis, EventBridge（CloudWatch Events）, SQS, SNS, CloudFormation Custom Resources など。
- 非同期呼び出しと同期呼び出しの違い: 非同期は再試行/バッファリング、同期は呼び出し元が応答を待つ。

## AWS AppSync

- AWS AppSync はフルマネージドな GraphQL API サービスで、クライアントが必要なデータだけを柔軟に取得できる。
- 主なデータソース:
  - DynamoDB
  - Lambda
  - OpenSearch Service
  - Aurora Serverless など
- 特徴:
  - GraphQL スキーマに基づいて単一エンドポイントで複数データソースを統合できる。
  - リアルタイム更新やオフライン同期に対応し、モバイルアプリや Web アプリとの相性が良い。
  - リゾルバで VTL または Lambda を使ってバックエンド処理を実装する。
- 主なユースケース:
  - モバイルアプリ向け API
  - 複数バックエンドをまとめる BFF
  - リアルタイム通知や共同編集アプリ
- API Gateway との使い分け:
  - REST API や HTTP API をシンプルに公開したい場合は API Gateway が適する。
  - クライアントごとに取得項目が異なり、複数データソースを GraphQL で統合したい場合は AppSync が適する。

## 実行モデルとリソース管理

- メモリ（128MB 〜 10,240MB）を設定すると CPU/ネットワーク帯域も比例して割り当てられる。
- タイムアウトは最大 15 分（900 秒）。長時間処理は Step Functions などを検討。

## Cold start とパフォーマンス最適化

- コールドスタートはランタイム初期化や依存関係ロード時に発生。対策: 軽量ランタイム、プロビジョンドコンカレンシー、関数の分割、依存関係の最適化。

## コンカレンシーとスロットリング

- 同時実行数の制限: アカウント単位のデフォルトリミットがあり、プロビジョンドコンカレンシーや予約済み同時実行で制御可能。
- Reserved Concurrency を設定すると特定関数の最大同時実行数を制限できる（他関数への影響を防ぐ）。

## レイヤーと共有ライブラリ

- Lambda Layer を使ってライブラリや共通コードを分離して再利用可能。デプロイアーティファクトを小さく保つのに有効。
- コンテナイメージ対応: 最大 10 GB のコンテナイメージを使用可能。OCI イメージでカスタムランタイムやバイナリを含められる。

## エラーハンドリングとデスティネーション

- 非同期呼び出しでは自動再試行 (最大 2 回) が行われ、失敗後に DLQ（SQS/SNS）や Lambda Destinations を使って結果を送ることができる。
- 同期呼び出しでのエラーは呼び出し元に即時返る。API Gateway などと組み合わせる際はタイムアウトとエラー処理を設計する。

## モニタリングとトレーシング

- CloudWatch Metrics（Invocations, Errors, Duration, Throttles, IteratorAge など）と CloudWatch Logs。
- AWS X-Ray を使って分散トレーシングを行い、レイテンシやボトルネックを可視化。

## ベストプラクティスと設計パターン

- ステートレス関数を基本とし、状態は DynamoDB / S3 / ElastiCache / Step Functions に委ねる。
- 関数の責務を小さく保ち、単一責任にすることでデプロイのリスクを低減する。
- 非同期処理は SQS と組み合わせて耐障害性を確保。メッセージの順序や重複処理は FIFO キューや冪等化で対応。

## IaC / CLI の例

```bash
# SAM テンプレートで簡単な Lambda をデプロイする典型的なフロー
sam init --runtime python3.9 --name my-lambda-app
sam build
sam deploy --guided
```

## 試験向けポイント / よく出るトピック

- コールドスタートの原因と対策（プロビジョンドコンカレンシー、ランタイム選択、関数分割）。
- 実行時間が長くなる処理は Step Functions の利用を検討する点。
- 非同期処理の再試行ポリシーや DLQ、Destination の使い分け。
- AppSync は GraphQL ベースの API サービスであり、DynamoDB や Lambda と組み合わせたサーバーレス API 設計で問われやすい。
- API Gateway は REST / HTTP API、AppSync は GraphQL API という役割の違いを整理しておく。

## サンプル問題

1. 高スループットのイベント処理で Lambda と SQS を組み合わせる場合、スロットリングやメッセージの順序、重複処理はどのように扱いますか？

2. コールドスタートを最小化するための具体策を 3 つ挙げ、それぞれの利点と欠点を説明してください。

3. モバイルアプリから DynamoDB と Lambda を組み合わせて柔軟な API を提供したい。API Gateway ではなく AppSync を選ぶ理由を説明してください。

参考リンク

- https://aws.amazon.com/lambda/
