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
