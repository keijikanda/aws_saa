# Data Analytics（Athena / Redshift / Kinesis / X-Ray）

# Athena

Amazon Athena は S3 上のデータに対して直接 SQL クエリを実行できるサーバーレスなインタラクティブクエリサービスです。

- **特徴**:
	- S3 に保存された CSV, JSON, Parquet, ORC など様々な形式のデータを直接分析可能
	- サーバーレスでインフラ管理不要、クエリ実行分だけ課金
	- 標準 SQL（Prestoベース）でクエリ記述
	- Glue データカタログと連携し、スキーマ管理やパーティション分割も容易
- **主なユースケース**:
	- ログやデータレイクのアドホック分析
	- S3 に蓄積した CloudTrail ログの検索
	- データサイエンスや BI の前処理

## CLI 例
```bash
# Athena クエリの実行
aws athena start-query-execution --query-string "SELECT * FROM my_table LIMIT 10;" --result-configuration OutputLocation=s3://my-bucket/athena-results/
```

# Redshift

Amazon Redshift はペタバイト級のデータウェアハウスを構築できるフルマネージドな高速分析サービスです。

- **特徴**:
	- 列指向ストレージによる高速クエリ
	- マルチノード構成によるスケーラビリティと高可用性
	- S3 との連携（Redshift Spectrum）でデータレイクも直接分析可能
	- BI ツールや SQL クライアントから標準 SQL で利用可能
- **主なユースケース**:
	- 大規模な集計・分析処理（BI, レポーティング）
	- データウェアハウスとしての利用
	- S3 データレイクとのハイブリッド分析

## CLI 例
```bash
# Redshift クラスターの作成
aws redshift create-cluster --cluster-identifier my-redshift --node-type ra3.large --master-username admin --master-user-password mypassword --number-of-nodes 2
```

## 参考リンク
- https://aws.amazon.com/jp/athena/
- https://aws.amazon.com/jp/redshift/

# Amazon Macie

Amazon Macie は、機密データ（PIIなど）が S3 バケットに存在するかを検出・分類し、データ保護のリスクを可視化するマネージドサービスです。

- **主な機能**:
	- S3 オブジェクトの自動スキャンと機密情報（クレジットカード番号、個人識別情報など）の検出
	- データアクセスパターンの異常検知（異常なアクセス頻度や外部アクセス時の通知）
	- カスタム検出ルールの定義が可能
	- レポートとダッシュボードによるリスク・コンプライアンス管理
- **データソース**:
	- S3 のみ（データレイク領域に保存されるデータ保護を補完）
- **ユースケース**:
	- GDPR、CCPA、PCI DSSなどに準拠するための機密データ監査
	- 重要データの意図しない公開・漏えいの早期検知
	- データガバナンスの強化
- **料金**:
	- スキャン対象の S3 オブジェクト量と検出ルール実行数に基づく課金
	- 管理対象バケット数とスキャン時間がコスト要素

## 参考リンク
- https://aws.amazon.com/jp/macie/

# Amazon DataZone

Amazon DataZone はデータカタログ、ガバナンス、共有を包括的に管理するデータドメインサービスです。データプロバイダーとデータコンシューマーの役割を明確化し、企業内外のデータアクセスを制御して活用を促進します。

- **主な機能**:
	- セルフサービス型データディスカバリ（データカタログ、タグ、メタデータ）
	- データドメインとゾーンの構築（データソースの論理的グルーピング）
	- ガバナンス定義（データ共有ポリシー、承認ワークフロー、アクセス制御）
	- データ品質管理（検証ルール、承認されたデータ資産）
	- IAM や AWS Lake Formation との連携による権限管理
- **関連サービス**:
	- AWS Lake Formation: セキュアなデータレイク権限設定、テーブル/列レベルのアクセス制御
	- AWS Glue Data Catalog: メタデータの中央リポジトリ化とETL処理連携
	- AWS IAM / AWS Organizations: クロスアカウントデータ共有とポリシー一元管理
	- Amazon S3 / Amazon Redshift / Amazon Athena: データソースエンドポイントとして統合
- **ユースケース**:
	- 大規模組織におけるデータ製品管理と責任の明確化
	- データガバナンスとコンプライアンス対応（情報保護・監査）
	- データメッシュ実装の基盤としての利用

## 参考リンク
- https://aws.amazon.com/jp/datazone/

# Amazon EMR

Amazon EMR は、Apache Hadoop、Spark、Hive、HBase、Flink などのビッグデータフレームワークを AWS 上で実行するためのマネージドクラスタサービスです。

- **特徴**:
	- EC2 ベースで大規模データ処理クラスタを迅速に構築できる
	- Spark や Hadoop の実行環境をフルマネージドで提供
	- S3 をデータレイクとして利用し、計算リソースとストレージを分離できる
	- スポットインスタンスやオートスケーリングでコスト最適化しやすい
- **主なユースケース**:
	- 大規模 ETL
	- ログ集計やバッチ分析
	- 機械学習前処理
	- Hadoop / Spark ベースの既存ワークロード移行
- **補足**:
	- 長時間の分散処理や細かいチューニングが必要な場合に向く
	- サーバーレスに SQL だけ実行したいケースでは Athena の方がシンプル

## EMR の主要な実行形態

- `EMR on EC2`:
	- EC2 クラスタを自分で確保して実行する形態
	- カスタマイズ性が高く、長時間処理や継続利用向き
- `EMR Serverless`:
	- クラスタ管理不要で Spark / Hive ジョブを実行できる
	- 実行した分だけ課金され、断続的なジョブに向く
- `EMR on EKS`:
	- EKS 上で Spark ジョブを実行する形態
	- コンテナ基盤を統一したい場合に有効

# Apache Spark on AWS

Apache Spark は、大規模データを高速に分散処理するためのフレームワークです。AWS では主に Amazon EMR 上、または AWS Glue / EMR Serverless / EKS 上で利用されます。

- **特徴**:
	- インメモリ処理により Hadoop MapReduce より高速なケースが多い
	- バッチ処理、SQL、ストリーム処理、機械学習を幅広くサポート
	- `Spark SQL`、`Structured Streaming`、`MLlib` などの機能を持つ
- **AWS での代表的な使い方**:
	- S3 のデータを Spark で ETL し、Redshift や S3 に保存
	- Kinesis や Kafka 由来のデータをストリーム処理
	- Glue Data Catalog と連携してテーブル定義を共有
- **SAA 向けの選び分け**:
	- 単純な SQL 分析なら Athena
	- 大規模分散 ETL や独自ロジックを含む処理なら Spark / EMR
	- DWH として高速集計したいなら Redshift

## 試験向けポイント

- `EMR` は Hadoop / Spark などのビッグデータ基盤を実行するサービス。
- `Spark` 自体は分析エンジンであり、AWS のマネージドサービス名ではない。
- 「大規模データを分散 ETL」「既存の Hadoop / Spark ジョブを実行」「細かい実行制御が必要」といった要件では EMR が有力。
- 「サーバーレスに S3 へ SQL を投げたい」だけなら Athena の方が適切。

# Kinesis

Amazon Kinesis はリアルタイムで大量のデータストリームを収集・処理・分析できるフルマネージドサービス群です。

- **主なサービス群**:
	- **Kinesis Data Streams**: 高スループットでリアルタイムデータを取り込み、複数のコンシューマーで並列処理可能。IoT、アプリログ、クリックストリーム等の用途。
	- **Kinesis Data Firehose**: 取り込んだデータを S3, Redshift, Elasticsearch, Splunk などに自動配送。バッチ処理不要で ETL もサポート。
	- **Kinesis Data Analytics**: SQL でストリームデータをリアルタイム分析。ウィンドウ集計やパターン検出などが可能。
	- **Kinesis Video Streams**: 動画・音声ストリームの収集・保存・分析。
- **特徴**:
	- 高いスケーラビリティと耐障害性（シャード単位でスケール）
	- サーバーレスで運用負荷が低い
	- Lambda など他 AWS サービスと連携しやすい
- **代表的なユースケース**:
	- IoT センサーデータのリアルタイム集計
	- Web/アプリのクリックストリーム分析
	- ログ・メトリクスのリアルタイム監視

## CLI 例
```bash
# Kinesis Data Stream の作成
aws kinesis create-stream --stream-name my-stream --shard-count 2
# Firehose 配信ストリームの作成
aws firehose create-delivery-stream --delivery-stream-name my-firehose --s3-destination-configuration ...
```

## 参考リンク
- https://aws.amazon.com/jp/kinesis/

# AWS X-Ray

AWS X-Ray は分散トレーシングサービスで、マイクロサービスや分散アプリケーションのリクエストの流れを可視化・分析できます。

- **主な用途**: アプリケーションのパフォーマンスボトルネックや遅延、障害箇所の特定。リクエストが複数のサービス（例: API Gateway, Lambda, ECS, RDS など）をまたぐ場合でも、エンドツーエンドでトレース可能。
- **仕組み**: 各サービスやアプリケーションに X-Ray SDK/エージェントを組み込み、リクエストごとにトレースデータ（セグメント/サブセグメント）を収集。これを X-Ray サービスに送信し、可視化・分析する。
- **主な機能**:
	- サービスマップ: 各サービス間の依存関係や遅延をグラフィカルに表示
	- トレース分析: 個別リクエストの詳細な処理経路やレイテンシを確認
	- エラー/例外の検出: どのサービスで障害が発生したかを特定
- **統合例**: Lambda, API Gateway, ECS, EC2, Elastic Beanstalk など多くの AWS サービスと統合可能。
- **コスト**: トレースデータの収集・保存量に応じて課金。

## 代表的なユースケース
- マイクロサービス間の遅延や障害の特定
- サーバーレスアプリケーションのパフォーマンス分析
- API Gateway から Lambda、DynamoDB までの一連のリクエスト経路の可視化

## CLI 例
```bash
# X-Ray グループの作成
aws xray create-group --group-name MyAppGroup
# X-Ray サンプリングルールの作成
aws xray create-sampling-rule --sampling-rule '{"RuleName":"MyRule","Priority":1,"FixedRate":0.05,"ReservoirSize":1,"ServiceName":"*","ServiceType":"*","Host":"*","HTTPMethod":"*","URLPath":"*","Version":1}'
```

## 参考リンク
- https://aws.amazon.com/jp/xray/
