# KMS & Encryption

概要

- AWS Key Management Service (KMS) は暗号鍵の作成・管理・使用を安全に行うサービスで、他の AWS サービスと統合してデータ暗号化を実現します。

## KM S の基本要素

- CMK（Customer Master Key / KMS キー）: 対称鍵を KMS が管理する。キーの種類には AWS 管理キー、顧客管理キー（CMK）、顧客提供キー（CSEK/SSE-C）などがある。
- キーポリシー: キーに対するアクセスを定義する最も強力なポリシー。
- Grants: 一時的にキー使用を許可するための小さな委譲機構。

## 暗号化パターン

- サービス統合: S3, EBS, RDS, Redshift, Kinesis などは KMS と統合してサーバーサイド暗号化を提供。
- Envelope Encryption: データキー（Data Key）を生成し、データ本体はこのデータキーで暗号化、データキー自体は CMK で暗号化する方式。パフォーマンスとセキュリティの両立に有効。

## 管理と運用

- キーの自動回転: KMS は年間単位で自動回転をサポート（対称 CMK のみ）。
- CloudHSM: 高いセキュリティ要件がある場合、CloudHSM を利用してカスタムの鍵管理を行う。
- 監査: CloudTrail で KMS API の使用ログを取得し、誰がどのキーを使ったかを監査可能。

## ベストプラクティス

- 最小権限の原則でキーポリシーを設計。
- 機密性の高い操作には追加の認可（IAM + キーポリシー）を組み合わせる。
- Grants を使って短期のアクセスを委譲するパターンを検討。

## CLI / IaC の例

```bash
# KMS キーの作成（対称 CMK）
aws kms create-key --description "My CMK" --tags TagKey=Project,TagValue=MyApp

# データキーの生成（Envelope Encryption 用）
aws kms generate-data-key --key-id alias/MyKey --key-spec AES_256 --query CiphertextBlob --output text
```

## 試験向けポイント / よく出るトピック

- KMS と CloudHSM の用途の違い（CloudHSM は FIPS レベルや専用ハードウェア要件に利用）。
- Envelope Encryption のメリット（パフォーマンスとセキュリティ）。

## サンプル問題

1. S3 のバケットを暗号化する際、SSE-S3、SSE-KMS、SSE-C の選択肢とそれぞれの利点・制約を説明してください。

参考リンク

- https://aws.amazon.com/kms/
