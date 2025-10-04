# CloudFormation & CDK

概要

- AWS CloudFormation は JSON / YAML テンプレートでリソースを宣言的に定義し、スタック単位でデプロイ・更新するツールです。AWS CDK は高レベル言語（TypeScript / Python / Java / C# / JavaScript など）で CloudFormation テンプレートをプログラム的に生成するフレームワークです。

## CloudFormation の基本

- テンプレート構成: Parameters, Mappings, Resources, Outputs, Conditions, Transform（マクロ）
- Change Sets: スタック更新前に差分を検査できる。破壊的変更を検出し確認できる。
- Drift Detection: ランタイムで実際のリソースとテンプレートの差分を検出する。

## モジュール化と再利用

- Nested Stacks / Modules / Macros を使ってテンプレートを分割し再利用可能にする。
- マクロや Transform を使うとテンプレートをプログラム的に拡張可能（例: AWS::Serverless-2016-10-31）。

## CDK の概念

- Construct: 再利用可能なリソースの集合。
- Stack: デプロイ単位。App の中に複数の Stack を置ける。
- App: CDK アプリケーションのエントリポイント。

## デプロイ戦略とロールバック

- スタック更新時に Create/Update/Delete の差分を確認して適用。失敗時は自動ロールバックや Change Set を利用した手動管理が可能。
- Blue/Green や Canary デプロイは CloudFormation + Route53 / ELB / CodeDeploy を組み合わせて実装する。

## テストと品質管理

- テンプレートの構文チェック、Lint、Unit Test（CDK では snapshot テスト）を導入。
- CI/CD パイプラインで change-set の作成・レビュー・承認フローを組むと安全。

## IaC のベストプラクティス

- テンプレートは小さく分割し、環境毎にパラメータ化する。
- 機密情報は SSM Parameter Store / Secrets Manager を使用し、テンプレートに直接書かない。
- 生成されるリソースの命名規則とタグ付けを統一して運用しやすくする。

## CLI / 例

```bash
# CloudFormation: Change Set を作成して確認
aws cloudformation create-change-set --stack-name my-stack --template-body file://template.yaml --change-set-name my-change
aws cloudformation describe-change-set --change-set-name my-change --stack-name my-stack

# CDK: synth -> deploy
cdk synth
cdk deploy
```

## 試験向けポイント / よく出るトピック

- Change Set とロールバックの使い分け、Drift Detection の目的。
- CDK の抽象化は便利だが、生成されたテンプレートを理解しておくこと（セキュリティ / コストの観点）。
- CloudFormation のIAMロールの使い方（実行ロール / 信頼関係）や、スタックポリシーの概念。

## サンプル問題

1. CloudFormation の Change Set を使う利点を 3 つ挙げ、それぞれ具体例を示してください。

2. CDK で複数リージョンにデプロイする際の注意点を述べてください（環境／アカウントごとの設定など）。

参考リンク

- https://aws.amazon.com/cloudformation/
- https://aws.amazon.com/cdk/
