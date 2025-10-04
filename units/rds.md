# RDS
概要

- Amazon Relational Database Service (RDS) はフルマネージドなリレーショナルデータベースサービス。バックアップ、自動パッチ適用、スケーリング（読み取りレプリカ）、監視など運用負荷を軽減する機能を提供します。

## サポートエンジンと特徴

- MySQL / MariaDB / PostgreSQL / Oracle / SQL Server をサポート。
- Amazon Aurora: MySQL / PostgreSQL 互換のマネージドデータベース。高性能でスケーラビリティに優れる（分離されたストレージレイヤ、クラスタ・リードレプリカの容易さ）。

## 可用性とスケーリング

- Multi-AZ 配置: プライマリと同期レプリカを別 AZ に配置し、自動フェイルオーバーを提供。書き込みワークロードに対して高可用性を実現。
- リードレプリカ: 読み取りスケールアウトに使用。非同期レプリケーションのためフェイルオーバーには向かない（ただし Aurora の場合はクラスタ構成で異なる）。

## ストレージとパフォーマンス

- ストレージタイプ: 汎用 SSD (gp3/gp2)、プロビジョンド IOPS (io1/io2)、磁気。
- IOPS とスループット要件に応じて選定。プロビジョンド IOPS は高性能な OLTP ワークロード向け。

## バックアップと復旧

- 自動バックアップ: 指定の保持期間で自動的にフルバックアップとトランザクションログによる PITR (ポイントインタイムリカバリ) を行う。
- スナップショット: 手動スナップショットは保持期間に関係なく保存可能（手動削除が必要）。

## セキュリティ

- VPC 内配置が推奨。セキュリティグループでアクセス制御。
- 暗号化: RDS ではストレージ暗号化（KMS）を選択可能。透過的データ暗号化を提供。
- IAM ポリシーと DB 認証: IAM 認証が使えるエンジンもある。

## 運用とモニタリング

- CloudWatch Metrics: CPU, FreeStorageSpace, DatabaseConnections, ReadReplicaLag などを監視。
- Enhanced Monitoring や Performance Insights で詳細メトリクスを収集。

## マイグレーション

- AWS Database Migration Service (DMS) を使ってダウンタイムを最小化してデータを移行可能。ソースとターゲットの互換性やキャラクタセットに注意。

## CLI / IaC の例

```bash
# 単純な RDS インスタンス作成の例
aws rds create-db-instance \
	--db-instance-identifier mydb \
	--db-instance-class db.t3.medium \
	--engine mysql \
	--allocated-storage 20 \
	--master-username admin \
	--master-user-password "P@ssw0rd" \
	--backup-retention-period 7 \
	--vpc-security-group-ids sg-01234567

# マルチ AZ オプションを有効にするには --multi-az を付与
```

## 試験向けポイント / よく出るトピック

- Multi-AZ はフェイルオーバー向け（高可用性）、リードレプリカは読み取りスケーリング向けである点を理解する。
- Aurora は RDS より高速でスケールが容易。Aurora のストレージは分離されており自動拡張が可能。
- バックアップ戦略（自動バックアップ、スナップショット、PITR）を設計できること。

## サンプル問題

1. ミッションクリティカルなデータベースを構築する場合、RDS のどの機能を使って可用性とデータ保護を確保しますか？具体的に説明してください（Multi-AZ, バックアップ, スナップショット, 暗号化 を含める）。

2. オンプレ MySQL を Aurora MySQL に移行したい。DMS を使う場合の基本的なステップと注意点を述べてください。

参考リンク

- https://aws.amazon.com/rds/

概要

- Amazon Relational Database Service (RDS) はフルマネージドなリレーショナルデータベースサービス。

重要ポイント

- サポートされるエンジン (MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, Amazon Aurora)
- マルチ AZ 配置とリードレプリカ
- ストレージタイプ (汎用 SSD, プロビジョンド IOPS)
- バックアップとスナップショット、ポイントインタイムリカバリ
- セキュリティ (VPC 内配置、セキュリティグループ、IAM を使った管理)

学習タスク

- マルチ AZ を有効にした RDS の作成
- リードレプリカを使った負荷分散とフェイルオーバーのシナリオ学習

練習問題

1. Aurora と RDS の違いをまとめ、ユースケースを考えてください。

参考リンク

- https://aws.amazon.com/rds/
