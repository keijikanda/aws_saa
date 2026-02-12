# KMS & Encryption

概要

- AWS Key Management Service (KMS) は暗号鍵の作成・管理・使用を安全に行うサービスで、他の AWS サービスと統合してデータ暗号化を実現します。

## KMS の基本要素

- **CMK（Customer Master Key / KMS キー）**: 対称鍵を KMS が管理する。キーの種類には AWS 管理キー、顧客管理キー（CMK）、顧客提供キー（SSE-C など）などがある。
- **キーポリシー**: キーに対するアクセスを定義する最も強力なポリシー。
- **Grants**: 一時的にキー使用を許可するための小さな委譲機構。

## 暗号化パターン

- **サービス統合**: S3, EBS, RDS, Redshift, Kinesis などは KMS と統合してサーバーサイド暗号化を提供。
- **Envelope Encryption**: データキー（Data Key）を生成し、データ本体はこのデータキーで暗号化、データキー自体は CMK で暗号化する方式。パフォーマンスとセキュリティの両立に有効。

## 管理と運用

- **キーの自動回転**: KMS は年間単位で自動回転をサポート（対称 CMK のみ）。
- **監査**: CloudTrail で KMS API の使用ログを取得し、誰がどのキーを使ったかを監査可能。

## CloudHSM（詳細）

- **概要**: AWS CloudHSM はお客様専有のハードウェアセキュリティモジュール（HSM）をクラウド上で提供するマネージドサービスです。FIPS 140-2 Level 3 相当のハードウェアセキュリティ、専有クラスタ、そしてお客様が鍵の生成・管理を直接制御できます。
- **主な特徴**:
	- 専有 HSM：クラスタはお客様専有で、他テナントとハードウェアを共有しません。
	- アルゴリズムと API：PKCS#11、Java JCE、Microsoft CNG など標準 API に対応し、幅広い暗号操作を HSM 内で実行できます。
	- キーの取り扱い：秘密鍵は HSM からエクスポートできない（設計により保護）。
	- 運用負荷：HSM クラスタの作成、HSM の追加、ソフトウェア初期化、バックアップ管理など運用タスクが必要です。

- **導入の流れ（概念）**:
	1. CloudHSM クラスタを作成し、各 AZ にサブネットを割り当てる
	2. HSM をプロビジョニングしてクラスタを初期化する
	3. HSM ユーザー（crypto user）を作成して鍵を生成・管理する

## KMS と CloudHSM の使い分け（詳細）

- **KMS の利点**:
	- フルマネージドかつ AWS サービス統合済み（S3, EBS, RDS などでそのまま利用可能）。
	- IAM とキーポリシーによる細かいアクセス制御、CloudTrail による監査が容易。
	- 自動スケーリングと可用性を AWS が管理するため、運用負荷が低い。
	- コストは CloudHSM に比べて一般的に低い（ただし利用量に依存）。

- **CloudHSM の利点**:
	- 専有 HSM による高いコンプライアンス要件（FIPS 140-2 Level 3）を満たす場合に有利。
	- KMS がサポートしない特殊な暗号アルゴリズムやカスタム処理を HSM 内で実行したい場合に必要。
	- 鍵の完全な管理・所有を求められる法的/規制要件がある場合に適する。

- **注意点 / トレードオフ**:
	- 運用: CloudHSM はクラスタのライフサイクル管理やバックアップ、ソフトウェアアップデート等の運用が必要。KMS は管理負担が少ない。
	- コスト: CloudHSM は専有ハードウェア分の固定費が高く、長期的コストが上回ることが多い。
	- 機能性: KMS は多くの AWS サービスとシームレスに統合される一方、CloudHSM はアプリ側で HSM API を呼び出す実装が必要になることが多い。

- **ハイブリッド運用パターン**:
	- **KMS + カスタムキー格納 (Custom Key Store)**: KMS の API と使い勝手を維持しつつ、鍵の実体を CloudHSM に保存する「カスタムキー・ストア」を使う方法があります。これにより、KMS の利便性と CloudHSM の専有保護を両立できます。

- **選定ガイド（簡易）**:
	- 法規制や監査で専有 HSM が要求される → CloudHSM
	- AWS サービス統合が最優先で運用負荷を減らしたい → KMS
	- KMS のサポートするアルゴリズムで十分だが、鍵の所在をさらに厳密に制御したい → KMS のカスタムキー・ストア（CloudHSM 連携）を検討

## ベストプラクティス

- 最小権限の原則でキーポリシーを設計。
- 機密性の高い操作には追加の認可（IAM + キーポリシー／HSM のユーザ管理）を組み合わせる。
- Grants を使って短期のアクセスを委譲するパターンを検討。

## CLI / IaC の例（参考）

```bash
# KMS キーの作成（対称 CMK）
aws kms create-key --description "My CMK" --tags TagKey=Project,TagValue=MyApp

# CloudHSM はクラスタの作成や HSM のプロビジョニングが必要（概念的な流れ）
# aws cloudhsmv2 create-cluster --hsm-type hsm1.medium --subnet-ids subnet-aaa subnet-bbb
# aws cloudhsmv2 initialize-cluster --cluster-id cluster-xxxxxxxx
```

## 試験向けポイント / よく出るトピック

- KMS と CloudHSM の用途の違い（CloudHSM は FIPS レベルや専用ハードウェア要件に利用）。
- Envelope Encryption のメリット（パフォーマンスとセキュリティ）。

## サンプル問題

1. S3 のバケットを暗号化する際、SSE-S3、SSE-KMS、SSE-C の選択肢とそれぞれの利点・制約を説明してください。

参考リンク

- https://aws.amazon.com/kms/
- https://aws.amazon.com/cloudhsm/
