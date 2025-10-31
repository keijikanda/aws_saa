# Security Best Practices

概要

- AWS 環境でセキュリティを確保するためのベストプラクティスと検出/対応の基本パターン。

## アイデンティティとアクセス管理

- IAM の最小権限の徹底、ロールベースのアクセス、MFA の活用。
- ルートアカウントは最小限の利用に限定し、アクセスキーを持たせない。
- IAM Access Analyzer でクロスアカウントの共有を検出。

## ネットワークセキュリティ

- VPC 分離、セグメント化、セキュリティグループ（ステートフル）と NACL（ステートレス）の適切な利用。
- Bastion / Session Manager を利用して管理アクセスを制限。

## ロギングと監査

- CloudTrail、VPC Flow Logs、CloudWatch Logs を有効にして操作とネットワークの可視化を確保。
- ログは中央アカウントへ集約し、長期保管や監査を実施。

## 検出と自動化

- GuardDuty で脅威検出、Security Hub で複数の検出結果を統合、Inspector で脆弱性評価を実施。
- 異常検出時は EventBridge → Lambda / SNS 等で自動対応（例: 一時ブロックや通知）を行う。

## アプリケーション / データ保護

- データは転送中（TLS）と保管時（KMS 等）で暗号化する。
- Secrets Manager / Parameter Store で機密情報を安全に管理。

## インシデント対応

- インシデントレスポンス計画を用意し、検出 → 分離 → 修復 → 事後分析の流れを定義。
- 重要な操作は事前にロールで制御し、アクセスログで追跡可能にする。

## パッチ管理と構成管理

- Systems Manager Patch Manager を使って OS レベルのパッチを自動化。
- 構成は IaC（CloudFormation/CDK）で管理し、ドリフト検出を行う。

## 試験向けポイント / よく出るトピック

- GuardDuty / Security Hub / Inspector の用途の違い。
- ネットワーク層の防御（NACL と SG の違い、VPC エンドポイントでのトラフィック遮断）。

## サンプル問題

1. あるアカウントで未知の EC2 インスタンスが急増した場合、どのログやサービスをまず確認して初動対応を行いますか？

参考リンク

- https://aws.amazon.com/security/
