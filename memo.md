# memo

カテゴリごとにメモしていく

## デプロイ

### AWS CodeCommit

### AWS CodeBuild

### AWS CodeDeploy

### AWS X-Ray

### Amazon CloudWatch

### AWS CodePipeline

### Amazon API Gateway

### Amazon CloudFront

### Amazon CloudWatch

### Amazon DynamoDB

### Amazon DynamoDB Accelerator(DAX)

### Amazon EC2

### Amazon ECR（Elastic Container Registry）

### Amazon ECS（Elastic Container Service）

### Amazon EFS

### Amazon ElasticCache

### Amazon Fargate

### Amazon Glacier

### Amazon Kinesis

### Amazon RDS

### Amazon Route53

### Amazon S3

### Amazon SES

### Amazon SNS（Simple Notification Service）

### Amazon SQS

### Amazon Virtual Private Cloud

### Amazon Cognito

### Amazon SQS

### Auto Scaling

### AWS CloudFormation

### AWS CloudTrail

### AWS Elastic Beanstalk

### AWS KMS

### AWS Lambda

### AWS OpsWorks

### AWS Secrets Manager

### AWS Security Token Service（STS）

### AWS Step Functions

### Elastic Load Balancing


## Security, Identity & Compliance
### AWS Identity and Access Management（IAM）

#### [AWS Identity and Access Management (IAM) Part1](https://www.slideshare.net/AmazonWebServicesJapan/20190129-aws-black-belt-online-seminar-aws-identity-and-access-management-iam-part1)

話の内容

* [IDと認証情報の管理](IDと認証情報の管理)
* アクセス権限の管理

IAMとは

* AWSリソースをセキュアに操作するために、認証・認可の仕組みを提供するマネージド・サービス
* 各AWSリソースに対して別々のアクセス権限をユーザーごとに付与できる
* 多要素認証（Multi-Factor Authentication:MFA）によるセキュリティの強化
* 一時的な認証トークンを用いた権限の委任
* 他のIDプロバイダーで認証されたユーザーにAWSリソースへの一時的なアクセス
* 世界中のAWSリージョンで同じアイデンティティと権限を利用可能
  * データ変更は結果整合性を保ちながら全リージョンに伝搬
* AWS　IAM自体の利用は無料

AWSリソースにアクセスする仕組み

* AWSリソースにアクセスするものをプリンシパルという
  * ユーザーまたはグループ
  * ロール
  * アプリケーション
* プリンシパルは認証されて、リクエストの内容を確認され、認可できるか判定され、リクエストの中で指定しているアクション/オペレーションを対象のAWSリソースに行う
* 認証で確認されるもの
  * Authentication
  * AWS STS
  * MFA token
* リクエストの中身
  * アクション
  * リソース
  * プリンシパル
  * 環境データ
  * リソースデータ
* 認可
  * IDベースポリシー
    * Permissions
  * リソースベースポリシー
    * Amazon S3
    * AWS Lambda
  * その他ポリシー
    * permission boudary
    * service control policy
    * access control list
* アクション/オペレーション
  * IAM CreateUser
  * IAM DeleteUser
  * IAM GetUser
  * EC2 RunInstances
  * EC2 StartInstances
  * Amazon S3 CreateBucket
  * Amazon S3 ListBucket
  * ...
* AWSリソース
  * Permissions
  * Instances
  * Endpoints
  * Bucket with objects
  * ...

### IDと認証情報の管理

AWSアカウントのルートユーザー

* そのアカウントのすべてのAWSサービスとAWSリソース全てに完全なアクセス権を持つユーザー
* AWSマネジメントコーンソールへはAWSアカウントを作成したときのメールアドレス/パスワードでサインイン
* IAMで設定するアクセスポリシーではアクセス許可を制限できない
  * AWS Organizationsのサービスコントロールポリシー（SCP）によってサービスを制限可能
* 極力ルートユーザーを使用しない！
  * とはいえルートユーザーでの認証が必要なタスクもある


ベストプラクティス

* AWSアカウントのルートユーザーアクセスキーをロックする
* ここのIAMユーザーを作成
* ユーザーの強力なパスワードポリシーを設定
* アクセスキーを共有しない
* 特権ユーザーに対してMFAを有効化する

#### [AWS Identity and Access Management (IAM) Part2](https://www.slideshare.net/AmazonWebServicesJapan/20190130-aws-black-belt-online-seminar-aws-identity-and-access-management-aws-iam-part2)

話の内容

* 権限の委任
* IDと権限のライフサイクル管理
