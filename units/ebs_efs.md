# EBS & EFS

概要

- EBS は EC2 にアタッチするブロックストレージ、EFS は複数インスタンスで共有可能なフルマネージドのファイルストレージです。

## EBS の詳細

- ボリュームタイプ: gp3/gp2（汎用 SSD）、io1/io2（高 IOPS）、st1/sc1（スループット最適化）、磁気（旧）。
- IOPS とスループット: io1/io2 はプロビジョンド IOPS を設定可能。gp3 では IOPS とスループットを独立設定可能。
- スナップショット: 増分方式で S3 に保存。スナップショットから新しいボリュームを作成可能。
- 暗号化: KMS と統合してボリュームを暗号化。アタッチ先のインスタンスに透過的に提供される。

## EFS の詳細

- パフォーマンスモード: General Purpose（デフォルト）と Max I/O（大規模並列ワークロード向け）。
- ストレージクラス: Standard / One Zone / Infrequent Access など（ライフサイクルポリシーで移行可能）。
- マウントターゲット: 各サブネットにマウントターゲットを作成してアクセスを提供。

## 運用とコスト

- スナップショットやバックアップのライフサイクルを設計してコストと RTO を最適化。
- EFS はスループットに応じた課金が発生する（プロビジョンドスループット選択時など）。

## CLI / IaC の例

```bash
# EBS スナップショット作成
aws ec2 create-snapshot --volume-id vol-01234567 --description "daily-backup"

# EFS ファイルシステム作成
aws efs create-file-system --performance-mode generalPurpose --throughput-mode bursting
```

## 試験向けポイント / よく出るトピック

- EBS はボリューム単位で暗号化可能（KMS）であり、インスタンス間で共有できない点（共有は EFS を使う）。
- EFS は複数インスタンス間でのファイル共有が可能で高可用性を提供するが、レイテンシとコストに注意。

## サンプル問題

1. 高 IOPS を必要とするデータベース用ストレージを設計する際、EBS のどのボリュームタイプを選びますか？理由を述べよ。

参考リンク

- https://aws.amazon.com/ebs/
- https://aws.amazon.com/efs/
