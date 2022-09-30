# AWSの運用構成

## 全体図
![](image/監視運用.drawio.png)
運用において使用するAWSサービスの全体図です。複雑で分かりづらいと思うので分けて説明します。<br>
AWSの基本的なサービスについても説明します。<br>
## 監視ツール
監視において使用するOSS(オープンソースソフトウェア)とAWSサービスについて説明します。<br><br>

<img src="image/Prometheus.png" width="240"/>　　<img src="image/AMP.png" width="60" /><br><br>
`Prometheus`はオープンソースのモニタリングツールで、CNCF(Cloud Native Computing Foundation)のgraduatedプロジェクトです。<br>
>CNCFとはクラウドネイティブコンピューティング技術を推進する非営利団体で、CNCFのGraduatedプロジェクトはCNCFにより「成熟した」と認められたプロジェクトのことです。有名な例だとKubernetesが挙げられます。<br>

Prometheus Exporterからデータを収集し、PromQLという専用クエリ言語でメトリクスデータを柔軟に表示できます。<br>
`Grafana`が`Prometheus`の可視化ツールとしてよく利用されています。<br>
デメリットとしてはストレージ設計や構築、運用管理にコストがかかったり、障害時の調査や復旧が難しい等があげられますが、AWSのマネージドサービスである`Amazon Managed Service for Prometheus(AMP)`を利用することでデメリットを解決できます。<br><br>
<img src="image/Cortex.png" width="150" /><br>
※`AMP`ではストレージにCNCFのIncubatingプロジェクトである`Cortex`が使用されていますが、`AMP`を使用する上ではストレージを意識する必要がないので、`Cortex`に関しても特に意識する必要はありません。<br>


<img src="image/Grafana.png" width="200" />　　<img src="image/AMG.png" width="60" /><br><br>
`Grafana`はオープンソースのデータ可視化ツールで、可視化に特化しているため、他プロダクトが独自で用意しているダッシュボードよりも時系列グラフの可視化自由度が高いです。<br>
また、データソースとしてさまざまなデータを可視化できます。<br>
デメリットとしてはストレージ設計や構築、運用管理にコストがかかる等があげられますが、
AWSのマネージドサービスである`Amazon Managed Grafana(AMG)`を利用することでデメリットを解決できます。<br>

<img src="image/OpenSearch.png" width="200" />　　<img src="image/OpenSearchService.png" width="60" /><br><br>
`OpenSearch`は`ElasticSearch`と`Kibana`から派生したオープンソースのツールで、AWSによって開発されました。<br>
様々な形式のデータの収集・可視化が可能です。<br>
AWSのマネージメントサービスである`Amazon OpenSearch Service`を使用します。<br>
<img src="image/ElasticSearch.jpg" height="70" />　　<img src="image/Kibana.png" height="70" /><br>
※`ElasticSearch`はElastic社によって開発されたオープンソースの全文検索エンジンでログの収集によく利用されています。<br>
また、`Kibana`はElastic社によって開発された`ElasticSearch`の可視化ツールです。<br><br>

<img src="image/CloudWatch.png" width="60" /><br>
**CloudWatch**<br>
AWSサービスのメトリクスとログを収集・可視化するAWSのサービスです。

<img src="image/X-Ray.png" width="60" /><br>
**X-Ray**<br>
トレースを収集・可視化するAWSのサービスです。<br>

<img src="image/Athena.png" width="60" /><br>
**Athena**<br>
S3内のデータをSQLを利用して分析できるAWSのサービスです。`Glue`というサービスでS3のデータのテーブルを作成し`Athena`にてSQLでの分析が出来るようにします。
## Grafanaによる可視化と一元化
監視項目によってツールやサービスが分かれていると面倒なので`Grafana`のダッシュボードで出来るだけすべてを監視できるようにしています。<br>
ただ`Grafana`ですべてが監視出来るわけではないので、詳細を知りたい際には各々のツールやサービスを確認することになると思います。<br>
ダッシュボードが確認できるツール・サービスについては全体図にてダッシュボードマークをつけています。<br>
![](image/Grafana.drawio.png)
`Prometheus`・`CloudWatch`・`Zabbix`からメトリクス、<br>
`OpenSearch`からログ(＋セキュリティ関連のログ)、<br>
`X-Ray`からトレース、<br>
`Athena`からコストデータとサーバー情報を可視化します。<br>

## サーバー
サーバーとサーバーにインストールするツールについて説明します。<br>
<img src="image/サーバー.drawio.png" width="350" /><br>

### **仮想サーバー**
<img src="image/EC2.png" width="60" /><br>
**EC2**<br>
LinuxやWindowsなどの仮想サーバを作成できるサービスです。<br>
<img src="image/ECS.png" width="60" /><br>
**ECS**<br>
コンテナ化されたアプリケーションを簡単にデプロイ、管理、およびスケーリングできるサービスです。<br>
起動するサーバーのタイプには`EC2`と`Fargate`があり、どちらも使用しています。<br>
<img src="image/Fargate.png" width="60" /><br>
**Fargate**<br>
`Fargate`を利用するとコンテナをサーバーレスで実行することができます。そのためインスタンスの管理が不要になります。<br>


### **ツール** <br>
<img src="image/Fluentd.png" width="150" /><br>
`Fluentd`はアプリケーションなどからログデータを収集し、フィルタリングして複数の宛先に送信できるオープンソースのツールで、CNCFのgraduatedプロジェクトです。<br>
出力先として様々なサービスが用意されており、数百のプラグインが利用可能です。<br>

<img src="image/FluentBit.png" width="180" /><br>
`FluentBit`は`Fluentd`の軽量版。<br>
ECSではこちらを使用します。<br>

<img src="image/OpenTelemetry.png" width="190"/>　　<img src="image/ADOT.png" width="60" /><br><br>
`OpenTelemetry`はクラウドネイティブアプリケーションとインフラストラクチャから「メトリクス」「トレース」のキャプチャと成形、エクスポートをするオープンソースのツールで、CNCFのIncubatingプロジェクトです。<br>
`AWS Disto for OpenTelemetry(ADOT)`というAWSサポートのディストリビューションがあり、エクスポート先にAWSのサービスがサポートされています。<br>

<img src="image/PrometheusLogo.png" width="60" /><br>
**Prometheus Exporter**<br>
`Prometheus`用のメトリクスを収集するツールです。<br>
様々な種類のExporterが用意されており、以下がその例です。<br>

|||
|---|---|
|node_exporter|Linuxサーバの基本情報(topコマンド的な情報)を送信。|
|windows_exporter|Windowsサーバの基本情報(タスクマネージャー的な情報)を送信。|
|apache_exporter|Apacheの情報を送信。|
|blackbox_exporter|HTTPやTCPなどを介したエンドポイント情報を送信。Webサービスの死活監視に利用。|
|ssl_exporter|Webサーバに使用しているSSL証明書の情報を送信。SSL証明書の期限監視に便利。|

<img src="image/CloudWatch.png" width="60" /><br>
**CloudWatch Agent**<br>
`CloudWatch`用のメトリクス、ログを収集するエージェントです。<br>
基本的には`Prometheus`でメトリクスを収集しますが、`Auto Scaling`や`Compute Optimizer`では`CloudWatch`のメトリクスを利用するため、`CloudWatch`でも一部メトリクスを収集することになります。<br>

<img src="image/SSM.png" width="60" /><br>
**SSM Agent**<br>
`Systems Manager`用のエージェントです。<br>
インストールすることで、サーバーに対して`Systems Manager`の様々な機能が利用できます。<br>

<img src="image/Ansible.png" width="200" /><br>
`Ansible`は多数のサーバーや複数のクラウドインフラを統一的に制御できるオープンソースの構成管理ツールです。<br>
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
![](image/EC2監視.drawio.png)<br>
### ECSの監視
![](image/ECS監視.drawio.png)<br>
ECSでは、`OpenTelemetry`がECSのエージェントからコンテナのメトリクスを取得します。<br>
また、ECSにおいては`Fluentd`よりも`FluentBit`の利用が推奨されているため、`FluentBit`を使用します。<br>
ECSにて用意されている`FireLens`というログドライバーを使用することで、自動で`FluentBit`のサイドカーコンテナが用意されます。
## セキュリティ
セキュリティに関しては以下のようにAWS内で様々なサービスが用意されています。<br>
これらも可視化して監視できるようにしています。<br><br>
![](image/セキュリティ.drawio.png)<br>

### **保護**<br>
<img src="image/WAF.png" width="60" /><br>
**WAF**<br>
Webアプリケーションファイアウォールです。<br>
ベンダー提供のマネージドルールとユーザー定義ルールを組み合わせて、保護の条件を指定します。<br>

<img src="image/Shield.png" width="60" /><br>
**Shield**<br>
DDoS攻撃から保護します。<br>
Standardは無料でAdvancedは有料。Advancedはより強力に攻撃から保護することができます<br>

<img src="image/NetworkFirewall.png" width="60" /><br>
**Network Firewall**<br>
VPC向けのファイアーウォールです。<br>
`Network ACL`や`Security Group`に比べてより高度なファイアウォールを実装できます。<br>

<img src="image/NetworkACL.png" width="60" /><br>
**Network ACL**<br>
サブネット単位のファイアウォールです。<br>

**Security Group**<br>
インスタンス単位のファイアウォールです。<br>

<img src="image/FirewallManager.png" width="60" /><br>
**Firewall Manager**<br>
`WAF` `Shield` `Network Firewall` `Network ACL` `Security Group`を一元化します。<br>

<img src="image/KMS.png" width="60" /><br>
**KMS**<br>
暗号化操作に使用されるキーを簡単に作成および管理します。<br>

### **検知**<br>
<img src="image/CloudTrail.png" width="60" /><br>
**CloudTrail**<br>
AWSAccountに対する操作のイベントログを記録するサービスです。<br>
取得できる操作記録は「管理イベント」「データイベント」「インサイトイベント」の3種類です。<br>

<img src="image/Config.png" width="60" /><br>
**Config**<br>
AWSリソースやEC2インスタンス、オンプレミスサーバーの設定の変更管理、変更履歴のモニタリングを行うためのサービスです。<br>
また、設定に対するルールを指定でき、ルール違反した場合に`Systems Manager`のAutomationを実行させて、自動修復させることが出来ます。<br>

<img src="image/FlowLog.png" width="60" /><br>
**VPC Flow Log**<br>
VPC内のIPトラフィック状況をログとして保存できるVPCの機能です。<br>

<img src="image/AccessAnalyzer.png" width="60" /><br>
**IAM Access Analyzer**<br>
リソースのポリシーを確認して、意図せぬ公開設定などがされていないか検出し、可視化する機能です。<br>

<img src="image/GuardDuty.png" width="60" /><br>
**GuardDuty**<br>
`CloudTrail`や`VPC Flow Log`からAWS上で発生する不正やセキュリティイベントなどの脅威を検出するサービスです。<br>

<img src="image/Inspector.png" width="60" /><br>
**Inspector**<br>
SSMエージェントを導入したサーバーの脆弱性を検知するサービスです。<br>

<img src="image/Macie.png" width="60" /><br>
**Macie**<br>
S3バケット上にある個人情報等の機密データを自動的に発見し、通知や保護処理を実行するサービスです。<br>

<img src="image/SecurityHub.png" width="60" /><br>
**Security Hub**<br>
`GuardDuty` `Inspector` `Detective` `Macie` `IAM Access Analyzer` `Config`の検知内容を集約します。<br>
また、べストプラクティスや業界標準に基づいた、継続的なAWS環境の自動チェックも行います。<br>

<img src="image/TrustedAdvisor.png" width="60" /><br>
**Trusted Adviser**<br>
AWSのベストプラクティスの情報に基づいて、今設定されているものを自動的にをチェックし、推奨事項をレコメンドしてくれます。<br>

### **調査**<br>
<img src="image/Detective.png" width="60" /><br>
**Detective**<br>
`VPC Flow Log` `GuardDuty` `CloudTrail`から、
潜在的なセキュリティ問題や不審なアクティビティを分析、調査<br>

### 可視化
![](image/セキュリティログ.drawio.png)<br>
セキュリティログは`OpenSearch`に集約・可視化、`Grafana`に一元化します。<br>

## ネットワーク
<img src="image/VPC.png" width="60" /><br>
**VPC**<br>
AWS上に作成できるプライベート仮想ネットワーク空間です<br>

<img src="image/DirectConnect.png" width="60" /><br>
**DirectConnect**<br>
AWS への専用ネットワーク接続です。さくらインターネットと接続しています。<br>
<img src="image/VPN.png" width="60" /><br>
社内サーバーとはVPN接続で接続しています。<br>

<img src="image/Route53.png" width="60" /><br>
**Route53**<br>
AWSのDNSサービスです。ドメインのネームサーバーはこちらに移管済みです。<br>

<img src="image/CloudFront.png" width="60" /><br>
**CloudFront**<br>
コンテンツ配信ネットワーク (CDN) サービスです。<br>

<img src="image/ELB.png" width="60" /><br>
**ELB**<br>
ロードバランシングを行うサービスです。<br><br>

### 可視化
これらサービスのメトリクスとログも可視化します。<br>
<img src="image/ネットワーク.drawio.png" width="600" /><br>
ネットワークに関するメトリクスは`CloudWatch`で可視化し、`Grafana`に一元化、<br>
ネットワークに関するログは`OpenSearch`に集約・可視化、`Grafana`に一元化します。<br>

## コスト管理
以下のサービスを利用して、コストの監視と最適化を行います<br>

<img src="image/CostExplorer.png" width="60" /><br>
**Cost Explorer**<br>
AWSコストや使用料を可視化するダッシュボードサービスです。<br>
AWSアカウントをまたいだ分析やコスト算出もできます。<br>

<img src="image/Budgets.png" width="60" /><br>
**Budgets**<br>
AWSの使用料や予算額を管理するサービスです。<br>
予算をオーバーした際のメール通知が可能です。<br>

<img src="image/Cost&UsageReport.png" width="60" /><br>
**Cost and Usage Report**<br>
AWSのコストやリソースの使用状況を`S3`にアップロードします。<br>
`Glue`と`Athena`を使ってETL処理を行い、`Grafana`で可視化します。<br>

<img src="image/ComputeOptimizer.png" width="60" /><br>
**Compute Optimizer**<br>
`EC2`のCPU使用率などの利用状況を機械学習し、最適なインスタンスタイプを推奨してくれます。<br><br>

### 可視化
<img src="image/コスト管理.drawio.png" width="600" /><br>
`Budgets`と`Cost & Usage Report`のデータは`Grafana`で可視化します。<br>
## アカウント管理
`IAM Identity Center`と呼ばれるSingle Sign On（SSO）のサービスでログイン管理を行います。<br>
<img src="image/アカウント管理.drawio.png" width="500" /><br>
`IAM Identity Center`では複数のAWSアカウントと、`OpenSearch`、`Grafana`のログインを一元管理出来ます。<br>

## 運用の自動化
以下のサービスを利用して、運用を出来るだけ自動化させます。<br>
これらサービスのメトリクスも`CloudWatch`で取得できるので、自動化が失敗していないかどうか等の監視も行えます。<br>

<img src="image/Lambda.png" width="60" /><br>
**Lambda**<br>
コードをサーバーなしで実行できるサービスで、運用においても様々なLambda関数を利用します。<br>
言語はPythonかNode.jsが主流なので、現段階ではこれらの言語を使用しています。<br><br>
<img src="image/Lambda.drawio.png" width="250" /><br>
`EventBridge`や`SNS`をトリガーにして関数を実行できます。他にも様々なサービスをトリガーに出来ます。<br>

<img src="image/SNS.png" width="60" /><br>
**SNS**<br>
メールの送信だけでなく、`Lambda`の実行も行えるため、アラートに対しての自動対応が可能です。<br><br>
<img src="image/SNS.drawio.png" width="250" /><br>
様々なサービスから`SNS`にアラートを送信できます。

<img src="image/EventBridge.png" width="60" /><br>
**EventBridge**<br>
AWSのイベントやスケジュールを検知し、`Lambda`の実行や`SNS`を利用してのメール通知を行います。<br>
`Lambda`や`SystemsManager`のAutomationが実行出来るため、AWSでのイベントをトリガーにした自動処理を行えます。<br>

<img src="image/AutoScaling.png" width="60" /><br>
**Auto Scaling**<br>
CloudWatchアラームをトリガーにインスタンスを自動でスケーリングします。<br>

<img src="image/Backup.png" width="60" /><br>
**Backup**<br>
AWSサービスのバックアップのスケジュール管理やバックアップの保持期間の管理、バックアップに対するアクセスポリシーの設定を一元管理できます。<br>

<img src="image/DevOpsGuru.png" width="60" /><br>
**DevOps Guru**<br>
機械学習を使用して異常な動作パターンを検出するサービスです。

<img src="image/SSM.png" width="60" /><br>
**System Manager(SSM)**<br>
複数のサーバーに一括でコマンドを実行、複数の処理を一括実行、
サーバー上で稼働するソフトウェアの一覧を表示したりと、運用に関する複数の機能が利用できます。<br>
SSM Agentをサーバーに導入することで利用できます。オンプレのサーバーにも利用可能です。<br><br>
<img src="image/Automation.drawio.png" width="250" /><br>
`EventBridge`や`Config`をトリガーに`Systems Manager`のAutomationが実行できます。<br>
Configルールに違反していた際に、自動修復するなどの使い方ができます。<br><br>
<img src="image/インベントリ.drawio.png" width="600" /><br>
`Systems Manager`で取得できるサーバーのインベントリも`Glue`と`Athena`を利用して、`Grafana`で可視化ができます。<br>
## 構築
AWS内のリソースは基本的に`CloudFormation(CFn)`で構築し、`CodeCommit`でバージョン管理します。<br>
`CloudFormation(CFn)`を利用することで、AWSリソースをコードで管理できるので、現状を把握しやすくなりますし、<br>
`CodeCommit`を利用することで、変更差分がわかりやすく残るので、作業履歴として利用できます。<br>
さらに、`CodeBuild`と`CodePipline`を利用して、コミットから構築までを自動化させます。<br>
<img src="image/構築.drawio.png" width="500" /><br>

`CloudFormation(CFn)`のテンプレートを`CodeCommit`にコミットすると、
`CodePipline`により`CodeBuild`でテストされ、`CloudFormation`が実行されます。<br><br>
<img src="image/SAM.jpg" height="70" /><br>
同様に`Lambda`のコードを`CodeCommit`にコミットすると、`CodePipline`により`CodeBuild`でテストされ、`Serverless Application Model(SAM)`によって`CloudFormation`が実行され、作成された`Lambda`にコードのデプロイも行います。