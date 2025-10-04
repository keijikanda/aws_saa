# IAM

概要

- AWS Identity and Access Management (IAM) はユーザー、グループ、ロール、ポリシーを管理するサービス。
重要ポイント

- ポリシーの種類（マネージドポリシー、インラインポリシー）
学習タスク

- サービスロールを使った EC2 のアクセス制御を練習
練習問題

1. IAM ポリシーで特定の S3 バケットへのアクセスを許可するステートメントを設計してください。

参考リンク

- https://aws.amazon.com/iam/
# IAM

概要

- AWS Identity and Access Management (IAM) は AWS リソースへの認可・認証を管理するサービスです。ユーザー、グループ、ロール、ポリシー、フェデレーションなどの機能でアクセス管理を行います。

## 主要概念

- ユーザー: 個々の人やサービスアカウントを表すエンティティ。
- グループ: ユーザーの集合に対して一括でポリシーを付与するためのコンテナ。
- ロール: 信頼ポリシーによって一時的に権限を委譲できるエンティティ。サービスロールやクロスアカウントアクセスで使用。
- ポリシー: JSON 形式で Action / Resource / Effect / Condition を定義するアクセスルール。

## ポリシーの種類

- マネージドポリシー (AWS 管理 / カスタマー管理)
- インラインポリシー: エンティティ（ユーザー/ロール/グループ）に直結して付与される。

## ベストプラクティス

- 最小権限の原則: 必要な権限だけを付与する。
- IAM ロールを利用して長期のアクセスキーをユーザーや EC2 に置かない。
- 定期的な権限のレビューと CloudTrail ログでの監査。
- MFA の有効化（特にルートアカウント）。

## フェデレーションと SSO

- SAML / OIDC を使ったフェデレーションでオンプレの ID プロバイダや SaaS ディレクトリと連携。
- AWS SSO（現: IAM Identity Center）を利用した集中管理。

## 組織（AWS Organizations）と SCP

- Service Control Policies (SCP): 組織レベルで許可の上限を設定。リソースレベルでのポリシー（IAM ポリシー）とは別に機能する。

## 監査とモニタリング

- CloudTrail で API コールの記録を取得し、不審なアクセスを検出。
- Access Advisor や IAM Access Analyzer で利用されていない権限やリソース共有の問題を検出。

## CLI / IaC の例

```bash
# IAM ロール作成（信頼ポリシーは省略）
aws iam create-role --role-name MyRole --assume-role-policy-document file://trust-policy.json

# インラインポリシーをロールにアタッチ
aws iam put-role-policy --role-name MyRole --policy-name S3ReadOnly --policy-document file://s3-readonly.json
```

## 試験向けポイント / よく出るトピック

- 最小権限とロールの使い分け（サービスロール、インスタンスプロファイル、クロスアカウントロール）。
- SCP は deny を使って組織レベルの制約をかけるが、SCP 自体でアクセスを付与するものではない点。
- IAM ポリシーの Condition で IP 制限や MFA 要求を設定できる点。

## サンプル問題

1. あるチームに S3 の特定バケットを読み書きさせたい。IAM グループとポリシーでどのように設計しますか？（最小権限、バケットポリシーの考慮も含める）

2. 複数アカウント間でリソースを共有したい。クロスアカウントロールを使った実装手順を説明してください（信頼ポリシー、ポリシー付与の流れ）。

参考リンク

- https://aws.amazon.com/iam/
