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
  * AWS管理ポリシーを用いると多くのユースケースですぐにポリシーの適用を開始できる
  * 適切なアクセス権限を付与するにはIAMポリシーの詳細な知識が必要
  * まずはポリシードキュメントの扱いに慣れる
* インラインポリシーではなくカスタマー管理ポリシーを使用する
  * カスタマー管理ポリシーはカスタマイズ可能で再利用性も高く管理面で有利
  * カスタマー管理ポリシーの利点はすべての管理ポリシーを1箇所で感クニンできること
  * インラインポリシーの利用はできるだけ避ける
  * インラインポリシーはカスタマー管理ポリシーをに変換することが可能
* 追加セキュリティに対するポリシー条件を使用する
  * より安全なポリシーの運用のために、Condition要素によってポリシーが有効になる条件をさらに絞り込む
    * リクエストを許容するソースIPアドレスの範囲
    * 日付または時間の範囲
    * MFAデバイスでの認証の要求
    * SSLの使用の要求
* 最小権限を付与する
  * IAMポリシーを作成する場合、タスクの実行に必要なアクセス許可のみ付与する
  * 最小限のアクセス権限から開始し、必要に応じてアクセス権限を追加する
    * あとでアクセス権限を強化するより安全なアプローチ（※最初からゆるくしない）
  * 役立つ情報
  * アクセスアドバイザーのサービスの最小アクセス時間データ
  * Amazon CloudTrailのイベントログ
  * IAMポリシーをのリファレンス
* IAMユーザーへのアクセス許可を割り当てるためにグループを使用する
ポリシーの関連付けを簡単にするためにはIAMグループを利用したほうが便利
組織またはジョブ機能に関連したIAMグループを作成し、IAMグループに対してアクセスIAMポリシーを関連付ける
会社内で組織移動がある場合は、そのIAMユーザーが所属するIAMグループを変更すればよい

#### ポリシー

* IAMアイデンティティやAWSリソースに関連付けることによってアクセス許可を定義できるオブジェクト
* 通常、JSONポリシードキュメントでアクセス条件を記述
* ポリシードキュメントは1つ以上のStatementブロックで構成

#### AWSがサポートするポリシータイプ

* アイディンティティベースポリシー
  * 管理ポリシー
    * 複数のIAMユーザー、IAMグループ、IAMロールに関連付け可能（最大10個）
    * 再利用可能
    * 一元化された変更管理
    * バージョニングとロールバック
    * 種類
      * AWS管理ポリシー
        * AWSにより事前定義された管理ポリシー
        * AWSが作成および管理し、編集負荷
        * すべてのAWSアカウントで利用可能
          * AWSによる管理
            * AmazonEC2FullAccess
            * AmazonS3ReadOnlyAccess
          * ジョブ機能
            * AdministratorAccess
            * SecurityAudit
            * DataScientist
        * AWSにより更新される
      * カスタマー管理ポリシー
        * AWSアカウントで管理することができるカスタムポリシー
        * AWS管理ポリシーでは要件を満たせない場合にカスタマー管理ポリシーを適用
  * インラインポリシー
    * 1つのIAMエンティティ（単一のIAMユーザー、IAMグループ、IAMロール）に直接埋め込むポリシー
    * IAMエンティティに紐付いた固有のオブジェクト
    * IAMエンティティを削除するとインラインポリシーも削除される
    * IAMエンティティとポリシーとの厳密な1対1の関係を維持する必要がある場合などにインラインポリシーを適用する
* リソースベースポリシー
  * AWS IAMロールの信頼ポリシー
  * Amazon S3のバケットポリシー
  * Amazon SNSトピックのアクセス許可
  * Amazon SQSキューのアクセス許可
* パーミッションバウンダリー
  * AWS IAMアクセス許可の協会
  * AWS Organizationsサービスコントロールポリシー（SCP）
* アクセスコントロールポリシー
  * Amazon S3のバケットのACL
  * Amazon VPCのサブネットのACL
* セッションポリシー

#### ポリシードキュメントの主の要素

* Version:ポリシー言語のバージョン。Vesion要素を含めないとポリシー変数（${aws:username}など）は文字列として扱われる
* Statement:アクセス許可に関する複数要素（Effect/Action/Resourceなど）を含むステートメントブロック。複数のステートメントブロックを並べることができる
* Effect:AllowまたはDeny。ステートメントの結果を許可または明示的な拒否にするか指定する
* Principal:リソースベースのポリシー（バケットポリシーや信頼ポリシーなど）に記述する。リソースへのアクセスを許可または拒否するAMAエンティティ（IAMユーザー、フェデレーティッドユーザー（他システムと連携したユーザー？）、IAMロール、AWSサービスなど）をARN形式で指定する。
  * AWSリソースへのアクセスが許可/拒否されるIAMエンティティを指定する
  * リソースベースポリシーで使用
  * AWSアカウント、IAMユーザー、IAMロール、フェデレーティッドユーザー、引き受けたロールユーザー（assumed-role user）をARN形式で記述
    * ユーザーを指定する際にすべてのユーザーの意味でワイルドカード（*）を使用することはできない
  * IAMロールの信頼ポリシーのPrincipal要素に指定したIAMユーザーとIAMロールを削除すると信頼関係は壊れる。
    * 同じ名前でIAMエンティティを作成してもプリンシパルIDが異なるため、同じ名前で再作成した場合はロールの再編集が必要
* Action:Effect要素で許可または拒否する対象となる特定のアクションを記述する。大文字小文字の区別はされない。各AWSサービスを識別する名前空間（iam,ec2,s3など）でサポートされるアクションが定義されている。
  * 許可/拒否される特定のアクションを指定する
  * Statement要素にはAction/NotAction要素が必須
  * AWSサービスで行うことができるタスクを記述する独自のアクションセットを記述
  * 有効なアクション名はドキュメントを参照、またはポリシーエディターから選択
  * 形式："Action":"<各サービスの名前空間>":<アクション名>"
    * "Action":"ec2:StartInstances"
    * "Action":["sqs:SendMessage", "sqs:ReceiveMessage"]
    * "Action":"iam:*AccessKey"
    * "Action":IAM:listaccesskeys"
  * 注：複数のアクションを指定可能、ワイルドカード（*）を使用可能、値は大文字小文字の区別なし
* Resource:Action要素の対象となる特定のリソースをARN形式で記述する。指定したAction（ec2:DescribeInstancesなど）によっては個々のリソースを指定することができず、*（ワイルドカード）を指定する必要がある。
* Statement要素にはResource/NotResource要素が必須
* AWSサービスが持つ一連のリソースセットをARN形式で記述
  * 有効なリソースはドキュメントを参照、またはポリシーエディターから選択
  * "Resource":"arn:aws:sqs:us-east-2:123456789012:queue1"
  * "Resource":"arn:iam:123456789012:user/accounting/*"
  * "Resource":["arn:aws:dynamodb:us-east-2:123456789012:table/books_table",*   "arn:aws:dynamodb:us-east-2:123456789012:table/magagines_table"]
  * "Resource":"arn:aws:dynnamodb:us-easet-2:123456789012:table/${aws:username}"
* 注：複数のリソースを指定可能、ワイルドカード（*）を使用可能、JSONポリシー変数を指定可能
* Condition:ポリシーを実行する条件を指定することができる。Condition要素は条件演算子、ポリシー変数、条件値から構成される
  * ポリシーが有効になるタイミングの条件を指定する
  * Condition要素の記述はオプション
  * 条件キー：条件地に対する評価方法として条件演算子を作用させる演算式を記述
  * 形式：”Condition”:{<条件演算子>:{<条件キー>:<条件値>}}
  * 条件演算子
    * 条件比較のタイプ（文字列条件、数値条件、IPアドレス条件など）を指定する
    * 条件キーごとに使用できる条件演算子の種類が決まっている
  * 条件キー
    * AWSグローバル条件コンテキストキー（"aws:"で始まる）
      * すべてのサービスで使用可能なキー、一部のサービスでのみ使用可能なキーがある
    * AWSサービス固有のキー（そのサービス固有の名前（"s3":など）で始まる）
    * IAMの条件コンテキストキー
    * "Condition":{"StringEquals":{"aws:username":"johndoe"}}
    * "Condition":{"StringEqualsIgnoreCase":{"aws:username":"johndoe"}}
    * "Condition":{"IpAddress":{"aws:SourceIP:["192.0.2.0/24", "203.0.113.0/24"]}}
    * "Condition":{"StringEquals":{"ec2:ResourceTag/tagkey":"tagvalue"}}
    * "Condition":{"StringEquals":{"s3:prefix":"projects"}}
    * "Condition":{"StringEquals":{"iam:PassedToService":"cloudwatch.amazonaws.com"}}
    * "Condition":{"ForAllValues:StringEquals":{"dynamodb:Attributes:["ID", "Message", "Tags"]}}
  * 要素のAND条件OR条件
    * "Condition":{"DateGreaterThan":{"aws:CurrentTime:"2019-01-29T12:00:00Z"},"IpAddress":{"aws:SourceIp":["192.168.176.0/24","192.168.143.0/24]}
      * Condition下のブロックはAND条件、演算子に対する値はOR条件
      * この例の場合、"20219/1/29の12：00よりあとに、ソースIPアドレス"192.168.176.0/24"もしくは"192.168.143.0/24"のネットワークからアクセスしたリクエスト"を意味する
    * 注：条件キーは大文字小文字を区別しない、条件値の大文字小文字の区別は使用する条件演算子によって異なる
  * アクセス可否のロジック
    * 暗黙的なDeny（デフォルト）<明示的なAllow<明示的なDeny
    * すべてのアクセスはデフォルトでDeny
    * アクセス権限にAllowの条件があった場合、アクセス許可
    * ただし、アクセス権限に1つでもDenyの条件があった場合、アクセス拒否（明示的なDeny）
  * IAMポリシーの作成を支援するツール群がある

#### アクセス権の決定ロジック（同一アカウントの場合）

SCP AND (
    アイディンティティベースポリシー（管理ポリシー OR インラインポリシー）
    AND アクセス許可の境界
    ）
    OR リソースベースポリシー
)


* アイディンティティベースのポリシー
  * 管理ポリシーとインラインポリシーのそれぞれで許可されているものが有効な権限となる（OR条件）
* アクセス許可の境界
  * アクセス許可の境界とアイディンティティベースのポリシーの両方で許可されているものが有効な権限となる（AND条件）
* AWS Organizationsサービスコントロールポリシー（SCP）
  * SCPとアイデンティティベースのポリシーの両方で許可されているものが有効な権限となる（AND条件）
* リソースベースポリシー（同一アカウントの場合）
  * 同一アカウントの場合、リソースベースポリシーとアイディンティティベースポリシーのそれぞれで許可されているものが有効となる（OR条件）

#### アクセス権の決定ロジック（クロスアカウントの場合）

SCP AND (
    アイディンティティベースポリシー（管理ポリシー OR インラインポリシー）
    AND アクセス許可の境界
    ）
    AND リソースベースポリシー
)

リソースベースポリシー（クロスアカウントの場合）
クロスアカウントの場合、リソースベースポリシーとアイディンティティベースポリシーの療法で許可されているものが有効となる。

※アカウントが異なるリソースにアクセスをさせたい場合は、個別のアクセス許可（アイディンティティベースポリシー）が必要

### IAMグループ

* IAMグループ：IAMユーザーの集合
* IAMグループやIAMロールをIAMグループに所属させることは不可
* IAMユーザーは複数のIAMグループに所属することができる（最大10）
* IAMグループに関連付けられたIAMポリシーは所属するIAMユーザーに継承される

#### [AWS Identity and Access Management (IAM) Part2](https://www.slideshare.net/AmazonWebServicesJapan/20190130-aws-black-belt-online-seminar-aws-identity-and-access-management-aws-iam-part2)

話の内容

* 権限の委任
* IDと権限のライフサイクル管理

#### [IAM の仕組みについて](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/intro-structure.html#intro-structure-principal)

TODO