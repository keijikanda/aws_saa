# Cost Optimization

概要

- AWS のコスト最適化とは、正しい購入オプション、リソース最適化、運用自動化により総 TCO を下げることです。

## 主要手法

- 購入オプション: オンデマンド、リザーブドインスタンス（RI）、Savings Plans、スポットインスタンスの使い分け。
- Rightsizing: CloudWatch と Cost Explorer を使って利用状況を分析し、インスタンスタイプの見直しを行う。
- スケーリング: Auto Scaling による負荷に応じたリソース最適化。
- ストレージ階層化: S3 ライフサイクル（Standard -> IA -> Glacier 等）で保存コストを削減。

## ツールとガイド

- Cost Explorer: 使用量やコストの分析、予測。
- AWS Budgets: 予算とアラート設定。
- Trusted Advisor: コスト最適化の推奨（Idle リソース、RI の未購入など）。

## スポットと可用性

- スポットはコスト削減効果が高いが中断リスクがある。耐障害性の設計（ワークロードのチェックポイントや再実行）を入れる。
- 長期的に稼働するインスタンスは RI / Savings Plans を検討。

## タグ付けとコスト配分

- リソースに適切なタグを付けてコスト配分レポートを作成し、チーム別/プロジェクト別に最適化を図る。

## CLI / IaC の例

```bash
# Cost Explorer API は複雑なクエリになることが多く、コンソールや Cost and Usage Report の活用が推奨
aws ce get-cost-and-usage --time-period Start=2025-10-01,End=2025-10-31 --granularity MONTHLY --metrics "UnblendedCost" --group-by Type=DIMENSION,Key=SERVICE
```

## 試験向けポイント / よく出るトピック

- RI と Savings Plans の違い（柔軟性、適用範囲、割引の違い）。
- スポットの利点と中断時の設計対策。

## サンプル問題

1. 定常負荷で 24x7 稼働する Web サーバ群のコスト削減戦略を提案してください（RI/Savings/Reserved/Auto Scaling の組合せを含める）。

参考リンク

- https://aws.amazon.com/aws-cost-management/
