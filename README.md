# AWSの運用構成

## 全体図
![](監視運用.drawio.png)
運用において使用するAWSサービスの全体図です。複雑で分かりづらいと思うので分けて説明します。
## 監視ツール
監視において使用するOSS(オープンソースソフトウェア)とAWSサービスについて説明します。<br><br>
**Prometheus**<br><br>
<img src="Prometheus.png" width="150" /><br>
オープンソースのモニタリングツールで、CNCF(Cloud Native Computing Foundation)のgraduatedプロジェクトです。<br>
>CNCFとはクラウドネイティブコンピューティング技術を推進する非営利団体で、CNCFのGraduatedプロジェクトはCNCFにより「成熟した」と認められたプロジェクトのことです。有名な例だとKubernetesが挙げられます。<br>

Prometheus Exporterからデータを収集し、PromQLという専用クエリ言語でメトリクスデータを柔軟に表示できます。<br>
`Grafana`が`Prometheus`の可視化ツールとしてよく利用されています。<br>
デメリットとしてはストレージ設計や構築、運用管理にコストがかかったり、障害時の調査や復旧が難しい等があげられますが、AWSのマネージドサービスである`Amazon Managed Service for Prometheus(AMP)`を利用することでデメリットを解決できます。<br><br>
　　<img src="Cortex.png" width="60" /><br>
※`AMP`ではストレージにCNCFのIncubatingプロジェクトである`Cortex`が使用されていますが、`AMP`を使用する上ではストレージを意識する必要がないので、`Cortex`に関しても特に意識する必要はありません。<br>



**Grafana**<br><br>
<img src="Grafana.png" width="130" /><br>
オープンソースのデータ可視化ツールで、可視化に特化しているため、他プロダクトが独自で用意しているダッシュボードよりも時系列グラフの可視化自由度が高いです。<br>
また、データソースとしてさまざまなデータを可視化できます。<br>
デメリットとしてはストレージ設計や構築、運用管理にコストがかかる等があげられますが、
AWSのマネージドサービスである`Amazon Managed Grafana(AMG)`を利用することでデメリットを解決できます。<br>

**OpenSearch**<br><br>
<img src="OpenSearch.png" width="200" /><br>
`ElasticSearch`と`Kibana`から派生したオープンソースのツールで、AWSによって開発されました。<br>
様々な形式のデータの収集・可視化が可能です。<br><br>
<img src="ElasticSearch.jpg" width="150" /><img src="kibana.png" width="100" /><br>
※`ElasticSearch`はElastic社によって開発されたオープンソースの全文検索エンジンでログの収集によく利用されています。<br>
また、`Kibana`はElastic社によって開発された`ElasticSearch`の可視化ツールです。<br>


**X-Ray**<br>
トレースを収集・可視化するAWSのサービスです。<br>

**Athena**<br>
S3内のデータをSQLを利用して分析できるAWSのサービスです。`Glue`というサービスでS3のデータのテーブルを作成し`Athena`にてSQLでの分析が出来るようにします。
## Grafanaによる可視化と一元化
監視項目によってツールやサービスが分かれていると面倒なので`Grafana`のダッシュボードで出来るだけすべてを監視できるようにしています。<br>
ただ`Grafana`ですべてが監視出来るわけではないので、詳細を知りたい際には各々のツールやサービスを確認することになると思います。<br>
ダッシュボードが確認できるツール・サービスについては全体図にてダッシュボードマークをつけています。<br>
![](Grafana.drawio.png)
`Prometheus`・`CloudWatch`・`Zabbix`からメトリクス、<br>
`OpenSearch`からログ(＋セキュリティ関連のログ)、<br>
`X-Ray`からトレース、<br>
`Athena`からコストデータとサーバー情報を可視化します。<br>

## サーバー
サーバーにインストールするツールについて説明します。<br>
<img src="サーバー.drawio.png" width="350" /><br>

**Fluentd**<br>
<img src="Fluentd.png" width="150" /><br>
アプリケーションなどからログデータを収集し、フィルタリングして複数の宛先に送信できるオープンソースのツールで、CNCFのgraduatedプロジェクトです。<br>
出力先として様々なサービスが用意されており、数百のプラグインが利用可能です。<br>

**FluentBit**<br>
<img src="FluentBit.png" width="180" /><br>
Fluentd の軽量版。<br>
ECSではこちらを使用します。<br>

**OpenTelemetry**<br>
<img src="OpenTelemetry.png" width="130" /><br>
クラウドネイティブアプリケーションとインフラストラクチャから「メトリクス」「トレース」のキャプチャと成形、エクスポートをするオープンソースのツールで、CNCFのIncubatingプロジェクトです。<br>
`AWS Disto for OpenTelemetry(ADOT)`というAWSサポートのディストリビューションがあり、エクスポート先にAWSのサービスがサポートされています。<br>

**Prometheus Exporter**<br>
Prometheus用のメトリクスを収集するツールです。<br>
様々な種類のExporterが用意されており、以下がその例です。<br>

|||
|---|---|
|node_exporter|Linuxサーバの基本情報(topコマンド的な情報)を送信。|
|windows_exporter|Windowsサーバの基本情報(タスクマネージャー的な情報)を送信。|
|apache_exporter|Apacheの情報を送信。|
|blackbox_exporter|HTTPやTCPなどを介したエンドポイント情報を送信。Webサービスの死活監視に利用。|
|ssl_exporter|Webサーバに使用しているSSL証明書の情報を送信。SSL証明書の期限監視に便利。|

**CloudWatch Agent**<br>
`CloudWatch`用のメトリクス、ログを収集するエージェントです。<br>
基本的には`Prometheus`でメトリクスを収集しますが、`Auto Scaling`や`Compute Optimizer`では`CloudWatch`のメトリクスを利用するため、`CloudWatch`でも一部メトリクスを収集することになります。<br>

**SSM Agent**<br>
`Systems Manager`用のエージェントです。<br>
インストールすることで、サーバーに対して`Systems Manager`の様々な機能が利用できます。<br>

**Ansible**<br>
<img src="Ansible.png" width="130" /><br>
多数のサーバーや複数のクラウドインフラを統一的に制御できるオープンソースの構成管理ツールです。<br>
「Playbook」というファイルを利用して、サーバーに接続することなく、インストールや設定ファイルの更新等実行できます。<br>
`Systems Manager`のRun Commandという機能で「Playbook」が利用でき、ネット上で様々な「Playbook」を参照できるので活用しやすいです。<br>
ドメイン登録の際のApacheの設定ファイルの編集などで利用します。<br>

## サーバー監視
サーバーの監視においてはメトリクス・ログ・トレースの3つの監視が重要になります。<br>
- メトリクスはPrometheus　Exporterから取得したものを`OpenTelemetry`で`Prometheus`に送信し、`Grafana`で可視化します、<br>
- ログは`Fluentd`からAWSのサービスである`Kinesis Data Firehose`に送信することで、`S3`にリアルタイムストリーミングを行います。<br>
`S3`から`Lambda`を利用して成形後、`OpenSearch`に送信・可視化し、`Grafana`に一元化します。<br>
※ここで使用する`Lambda`は[SIEM on Amazon OpenSearch Service](https://github.com/aws-samples/siem-on-amazon-opensearch-service)を使用しています。<br>
- トレースは`OpenTelemetry`のSDKをアプリに導入することで、アプリから取得できるようになり、`OpenTelemetry`にて`X-Ray`用のデータに成形後、`X-Ray`に送信・可視化し、`Grafana`に一元化します。<br>
### EC2の監視
![](EC2監視.drawio.png)<br>
### ECSの監視
![](ECS監視.drawio.png)<br>
ECSにおいては`Fluentd`よりも`FluentBit`の利用が推奨されているため、`FluentBit`を使用します。<br>
ECSにて用意されている`FireLens`というログドライバーを使用することで、自動で`FluentBit`のサイドカーコンテナが用意されます。
## セキュリティ
セキュリティに関しては以下のようにAWS内で様々なサービスが用意されています。<br>
これらも可視化して監視できるようにしています。<br><br>
![](セキュリティ.drawio.png)<br>
### **保護**<br>
**WAF**<br>
Webアプリケーションファイアウォールです。<br>
ベンダー提供のマネージドルールとユーザー定義ルールを組み合わせて、保護の条件を指定します。<br>

**Shield**<br>
DDoS攻撃から保護します。<br>
Standardは無料でAdvancedは有料。Advancedはより強力に攻撃から保護することができます<br>

**Network Firewall**<br>
VPC向けのファイアーウォールです。<br>
Network ACLやSecurity Groupに比べてより高度なファイアウォールを実装できます。<br>

**Network ACL**<br>
サブネット単位のファイアウォールです。<br>

**Security Group**<br>
インスタンス単位のファイアウォールです。<br>

**Firewall Manager**<br>
「 WAF 」 「 Shield 」 「 Network Firewall 」 「 Route53 Resolver DNS Firewall 」「 Network ACL 」「 Security Group 」を一元化します。<br>

**KMS**<br>
暗号化操作に使用されるキーを簡単に作成および管理します。<br>

### **検知**<br>
**CloudTrail**<br>
AWSAccountに対する操作のイベントログを記録するサービスです。<br>
取得できる操作記録は「管理イベント」「データイベント」「インサイトイベント」の3種類です。<br>

**Config**<br>
AWSリソースやEC2インスタンス、オンプレミスサーバーの設定の変更管理、変更履歴のモニタリングを行うためのサービスです。<br>
また、設定に対するルールを指定でき、ルール違反した場合に`Systems Manager`のAutomationを実行させて、自動修復させることが出来ます。<br>

**VPC Flow Log**<br>
VPC内のIPトラフィック状況をログとして保存できるVPCの機能です。<br>

**IAM Access Analyzer**<br>
リソースのポリシーを確認して、意図せぬ公開設定などがされていないか検出し、可視化する機能です。<br>

**GuardDuty**<br>
「 CloudTrail 」や「 VPC Flow Log 」からAWS上で発生する不正やセキュリティイベントなどの脅威を検出するサービスです。<br>

**Inspector**<br>
エージェントを導入したサーバーの脆弱性を検知するサービスです。<br>

**Macie**<br>
S3バケット上にある個人情報等の機密データを自動的に発見し、通知や保護処理を実行するサービスです。<br>

**Security Hub**<br>
「 GuardDuty 」「 Inspector 」「 Detective 」「 Macie 」「 IAM Access Analyzer 」「 Config 」の検知内容を集約します。<br>
また、べストプラクティスや業界標準に基づいた、継続的なAWS環境の自動チェックも行います。<br>

**Trusted Adviser**<br>
AWSのベストプラクティスの情報に基づいて、今設定されているものを自動的にをチェックし、推奨事項をレコメンドしてくれます。<br>

### **調査**<br>
**Detective**<br>
「 VPC Flow Log 」「 GuardDuty 」「 CloudTrail 」から、
潜在的なセキュリティ問題や不審なアクティビティを分析、調査<br>

### ログの可視化
![](セキュリティログ.drawio.png)<br>
セキュリティログは`OpenSearch`に集約・可視化、`Grafana`に一元化します。<br>

## ネットワーク
ネットワークのメトリクスとログも可視化します。<br>
<img src="ネットワーク.drawio.png" width="600" /><br>
ネットワークに関するメトリクスは`CloudWatch`で可視化し、`Grafana`に一元化、<br>
ネットワークに関するログは`OpenSearch`に集約・可視化、`Grafana`に一元化します。<br>

## コスト管理
以下のサービスを利用して、コストの監視と最適化を行います<br>
<img src="コスト管理.drawio.png" width="600" /><br>

**Cost Explorer**<br>
AWSコストや使用料を可視化するダッシュボードサービスです。<br>
AWSアカウントをまたいだ分析やコスト算出もできます。<br>

**Budget**<br>
AWSの使用料や予算額を管理するサービスです。<br>
予算をオーバーした際のメール通知が可能です。<br>

**Cost and Usage Report**<br>
AWSのコストやリソースの使用状況を`S3`にアップロードします。<br>
`Glue`と`Athena`を使ってETL処理を行い、`Grafana`で可視化します。<br>

**Compute Optimizer**<br>
`EC2`のCPU使用率などの利用状況を機械学習し、最適なインスタンスタイプを推奨してくれます。<br>

## アカウント管理
`IAM Identity Center`と呼ばれるSingle Sign On（SSO）のサービスでログイン管理を行います。<br>
<img src="アカウント管理.drawio.png" width="500" /><br>
`IAM Identity Center`では複数のAWSアカウントと、`OpenSearch`、`Grafana`のログインを一元管理出来ます。<br>

## 運用の自動化
以下のサービスを利用して、運用を出来るだけ自動化させます。<br>
これらサービスのメトリクスも`CloudWatch`で取得できるので、自動化が失敗していないかどうか等の監視も行えます。<br>
<img src="自動化.drawio.png" width="350" /><br>

**Lambda**<br>
コードをサーバーなしで実行できるサービスで、運用においても様々なLambda関数を利用します。<br>
言語はPythonかNode.jsが主流なので、現段階ではこれらの言語を使用しています。<br>

**EventBridge**<br>
AWSのイベントやスケジュールを検知し、`Lambda`の実行や`SNS`を利用してのメール通知を行います。<br>
`SystemsManager`のAutomationが実行出来るため、サーバーに対しての自動処理を行えます。<br>

**SNS**<br>
メールの送信だけでなく、`Lambda`の実行も行えるため、アラートに対しての自動対応が可能です。<br>
`OpenSearch Service`と`Amazon Managed Grafana`、`CloudWatch`から`SNS`にアラートを送信できます。<br>

**Auto Scaling**<br>
インスタンスを自動でスケーリングします。<br>

**Backup**<br>
AWSサービスのバックアップのスケジュール管理やバックアップの保持期間の管理、バックアップに対するアクセスポリシーの設定を一元管理できます。<br>

**System Manager(SSM)**<br>
複数のサーバーに一括でコマンドを実行、複数の処理を一括実行、
サーバー上で稼働するソフトウェアの一覧を表示したりと、運用に関する複数の機能が利用できます。<br>
SSM Agentをサーバーに導入することで利用できます。<br><br>

<img src="インベントリ.drawio.png" width="600" /><br>
また、`Systems Manager`で取得できるサーバーのインベントリも`Glue`と`Athena`を利用して、`Grafana`で可視化ができます。<br>
## 構築
AWS内のリソースは基本的に`CloudFormation(CFn)`で構築し、`CodeCommit`でバージョン管理します。<br>
`CloudFormation(CFn)`を利用することで、AWSリソースをコードで管理できるので、現状を把握しやすくなりますし、<br>
`CodeCommit`を利用することで、変更差分がわかりやすく残るので、作業履歴として利用できます。<br>
さらに、`CodeBuild`と`CodePipline`を利用して、コミットから構築までを自動化させます。<br>
<img src="構築.drawio.png" width="500" /><br>

`CloudFormation(CFn)`のテンプレートを`CodeCommit`にコミットすると、
`CodePipline`により`CodeBuild`でテストされ、`CloudFormation`が実行されます。<br>
<br>
同様に`Lambda`のコードを`CodeCommit`にコミットすると、
`CodePipline`により`CodeBuild`でテストされ、
`Serverless Application Model(SAM)`によって`CloudFormation`が実行されます。