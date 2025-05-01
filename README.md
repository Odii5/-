AWS Serverless Architecture

プロジェクト概要
このプロジェクトでは、AWSを活用したフルスタックのデータ処理および監視システムを構築しています。主な機能としては、ウェブサイトのホスティング、API処理、非同期キューイング、データベース管理、データレイクへの蓄積、分析基盤、さらにリソースの監視と通知が含まれています。

使用技術スタックと構成
1. ウェブサイトホスティング（Amazon S3）
  ー　フロントエンドは、Amazon S3 を使用してホスティングしています。

  ー　S3上の静的ウェブサイトがユーザーと直接インターフェースを提供します。

  ー　ユーザーからの入力（データ）は、S3を通してAPI Gatewayに送信されます。

2. API処理（API Gateway → AWS Lambda）
  ー　Amazon API Gateway は、**Lambdaプロキシ統合（Proxy Integration）**で設定されています。

  ー　API Gatewayは受け取ったリクエストデータをLambda関数に渡します。

3. 非同期メッセージング（Lambda → Amazon SQS）
  ー　最初のLambda関数は、リクエストデータを処理した後、Amazon SQS（Simple Queue Service） にメッセージとして送信します。

  ー　この処理により、システムのスケーラビリティと耐障害性が向上します。

4. バックエンド処理と保存（SQS → Lambda → DynamoDB）
  ー　SQSキューにメッセージが追加されると、別のLambda関数がトリガーされます。

  ー　このLambda関数は、SQSメッセージを受け取り、データを処理し、Amazon DynamoDB テーブルに保存します。

5. データ変更の追跡（DynamoDB Streams）
  ー　DynamoDBではStreams機能を有効にしています。

  ー　テーブルに対する挿入・更新・削除イベントがストリームに記録されます。

  ー　ストリームイベントは、次の処理に渡されます。

6. イベントドリブン処理とデータレイクへの蓄積（Lambda → S3）
  ー　DynamoDB Streamsからのイベントをトリガーに、別のLambda関数が実行されます。

  ー　この関数はストリームデータを整形・加工し、CSV形式でAmazon S3のデータレイクに保存します。

7. データカタログとクエリ分析（AWS Glue + Amazon Athena）
  ー　S3に保存されたCSVファイルは、AWS Glue Data Catalogによりスキーマ管理されます。

  ー　Amazon Athenaを使用することで、S3内のCSVデータに対してSQLクエリによる分析が可能です。

8. セキュリティ監視と通知（CloudTrail → EventBridge → SNS）
  ー　ウェブホスティング用のS3バケットには、AWS CloudTrailを使って変更履歴を記録しています。

  ー　ポリシーや設定に変更があった場合、CloudTrailがイベントを生成します。

  ー　このイベントはAmazon EventBridgeルールによってキャッチされ、特定のアクション（条件）に一致する場合、

  ー　Amazon SNSトピックに通知が送信されます。

  ー　SNSは、あなたのメールアドレスに通知を送ります。


