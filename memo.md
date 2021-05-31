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

### Auto Scaling

### AWS CloudFormation

### AWS CloudTrail

### AWS Elastic Beanstalk

### AWS KMS

### AWS Lambda

### AWS OpsWorks

### AWS Secrets Manager

### AWS Security Token Service(STS)

### AWS Step Functions

### Elastic Load Balancing

## Security, Identity & Compliance

### AWS Identity and Access Management（IAM）

#### [AWS Identity and Access Management (IAM) Part1](https://www.slideshare.net/AmazonWebServicesJapan/20190129-aws-black-belt-online-seminar-aws-identity-and-access-management-iam-part1)

話の内容

* [IDと認証情報の管理](IDと認証情報の管理)
* アクセス権限の管理

IAMとは

* AWSリソースをセキュアに操作するための、認証・認可の仕組みを提供するマネージド・サービス
* 各AWSリソースに対して別々のアクセス権限をユーザーごとに付与できる
* 多要素認証（Multi-Factor Authentication:MFA）によるセキュリティの強化
* 一時的な認証トークンを用いた権限の委任
* 他のIDプロバイダーで認証されたユーザーにAWSリソースへの一時的なアクセス
* 世界中のAWSリージョンで同じアイデンティティと権限を利用可能
  * データ変更は結果整合性を保ちながら全リージョンに伝搬
* AWS　IAM自体の利用は無料

AWSリソースにアクセスする仕組み

* AWSリソースのアクションまたはオペレーションに対してリクエストできるユーザーまたはアプリケーションのこと（[IAM の仕組みについて](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/intro-structure.html#intro-structure-principal)から参照）
  * ユーザーまたはグループ
  * ロール
  * アプリケーション
* プリンシパルは認証されて、リクエストの内容を確認され、認可できるか判定され、リクエストの中で指定しているアクション/オペレーションを対象のAWSリソースに行う
* 認証で確認されるもの
  * Authentication
  * [AWS Security Token Service(STS)](#aws-security-token-servicests)
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

#### ベストプラクティス

* AWSアカウントのルートユーザーアクセスキーをロックする
  * ルートユーザーのアクセスキーは削除する
  * すでに持っている場合は削除する
  * ルートユーザーでサインインし、セキュリティ認証情報のページからアクセスキーを削除
    * `memo` ルートユーザーで発行していないか確認した→なかった。あと`ルートユーザーのアクセスキーは、AWS アカウント全体への無制限アクセスを提供します。長期的なアクセスキーが必要な場合は、制限されたアクセス許可を持つ新しい IAM ユーザーを作成し、そのユーザーのアクセスキーを生成することをお勧めします。`と書いていた。
  * ルートユーザーの認証情報を他社に開示したりプログラムに埋め込んだりしない
* 個々のIAMユーザーを作成
  * 個別のIAMユーザーを作成する
  * 必要な場合を除きAWSアカウントのルートユーザー認証情報を使用してAWSにアクセスしない
  * 個別のIAMユーザーを作成するメリット
    * 認証情報を個別に変更（ローテーション）できる
    * アクセス許可をいつでも変更、無効化できる
    * Amazon CloudTrailログからアクションを追跡できる
* ユーザーの強力なパスワードポリシーを設定
  * パスワードに要求される強度とパスワードポリシーを設定できる
    * 最小文字数少なくとも1つの英大文字が必要といった強度
    * パスワードの有効期限、ユーザー自身によるパスワード変更の許可、パスワード再利用禁止の世代数、管理者による期限切れパスワードのリセットなど
  * ルートユーザーのパスワードポリシーには適用されない
* アクセスキーを共有しない
  * 複数人がAWSリソースへのアクセス権を共有したい場合でもアクセスキーは共有しない
  * AWSへのアクセスを必要とするアプリケーションの場合はIAMロールを使用して一時的セキュリティ認証情報を取得する
  * アクセスキーは情報の置き場に注意する
    * GitHubリポジトリ、AMIへの埋め込み、ハードコーディング、プレーンテキストでの保管をするときは要注意
* 特権ユーザーに対してMFAを有効化する
  * ルートユーザーや強い権限を持つIAMユーザーにはMFAを有効化し、通常利用しないようにする
    * MFAデバイスも厳重に管理する
  * MFAデバイスの紛失/盗難/不具合が発生したら、代替の認証要素を使って認証し、新しいMFAデバイスを有効化し、パスワードも変更する

#### AWSアカウントのルートユーザー

AWSアカウントのルートユーザーとは

* そのアカウントのすべてのAWSサービスとAWSリソースすべてに完全なアクセス権を持つユーザー
* AWSマネジメントコーンソールへはAWSアカウントを作成したときのメールアドレス/パスワードでサインイン
* IAMで設定するアクセスポリシーではアクセス許可を制限できない
  * AWS Organizationsのサービスコントロールポリシー（SCP）によってサービスを制限可能
* 極力ルートユーザーを使用しない！
  * とはいえルートユーザーでの認証が必要なタスクもある

ルートユーザーの認証が必要なAWSタスクの例

* ルートユーザーのメールアドレスやパスワードの変更
* IAMユーザーによる課金情報へのアクセスのActivate/Deactivate
* 支払いオプションの変更
* AWSサポートプランの変更
* IAMユーザーへのアクセス許可のリストア
* 無効な制約を設定したAmazon S3バケットポリシーの修正
* 脆弱性診断フォームの提出
* 逆引きDNS申請
* CloudFrontキーペアの作成
* AWSアカウントの解約

アクセスキーとは

* AWSアカウントのルートユーザーまたはIAMユーザーの長期的な認証情報
  * 手動で取り消すまで有効
* アクセスキーとを用いてAWS CLIやAWS SDKなどからリクエストに署名
* アクセスキーID/シークレットアクセスキーで構成される
* 安全なローテーションのために、最大2つのアクセスキーを持つことができる


#### IAMユーザー

* AWSで作成するエンティティ（ユーザーまたはアプリケーション）
* 名前と認証情報で構成される
* IAMユーザーを識別する方法
  * ユーザーの「フレンドリ名」：ユーザー作成時に指定
    * 大文字小文字の区別はないためAcliceとaliceは統一とみなされる
  * ユーザーのARN（Amazon Resource Name）：リソースポリシーのPrincipal要素で指定する
    * 例：arn:aws:iam:01234565789012:user/Alice（ここでは数列とAliceと指定している）
  * ユーザーの一意の識別子：フレンドリ名を再利用したいと祈祷に権限のエスカレーションをさけることができる
    * 例：AIDAJQABLZS4A3QDU576Q
  * 認証情報
    * コンソールパスワード
    * アクセスキー

#### MFA（Multi-Factor Authentication：多要素認証）

* パスワードやアクセスキーによる認証に追加して、セキュリティを強化する仕組み
* AWSがサポートするMFAメカニズム
  * 仮想MFAデバイス：モバイルデバイスまたはコンピュータにインストールされたAuthenticatorアプリケーション（単一のデバイスで複数のトークンをサポート）
  * U2Fセキュリティキー：YubiKeyやその他のU2Fデバイス（単一のセキュリティキーで複数のルートユーザー/IAMユーザーをサポートしていて、手入力は不要）
  * ハードウェアMFAデバイス：Gelmatoトークン（単体でOTPを発行）
* ルートユーザー、IAMユーザーの各IDに個別のMFA設定が可能
* MFA条件をしてしたポリシーを関連付けできる対象
  * IAMユーザーまたはIAMグループ
  * Amazon S3バケット、Amazon SQSキュー、Amazon SNSトピックなどのリソース
  * IAMロールの信頼ポリシー

### アクセス権限の管理

#### ベストプラクティス

* AWS管理ポリシーを使用したアクセス許可の使用開始
* インラインポリシーではなくカスタマー管理ポリシーを使用する
* 追加セキュリティに対するポリシー条件を使用する
* 最小権限を付与する
* IAMユーザーへのアクセス許可を割り当てるためにグループを使用する

#### [AWS Identity and Access Management (IAM) Part2](https://www.slideshare.net/AmazonWebServicesJapan/20190130-aws-black-belt-online-seminar-aws-identity-and-access-management-aws-iam-part2)

話の内容

* 権限の委任
* IDと権限のライフサイクル管理

#### [IAM の仕組みについて](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/intro-structure.html#intro-structure-principal)

TODO