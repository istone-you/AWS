# AWSの運用構成

## **全体図**
<img src="image/監視運用.drawio.png" width="100%"/><br>
運用において使用しているAWSサービスの全体図です。
分かりづらいかと思うので、詳細な構成図とともに細かく分けて説明します。<br>
また、実際は監視されるアカウント（複数）と監視アカウントで分かれています。


## **OSS**
運用と監視において使用しているOSS(オープンソースソフトウェア)は以下の通りです。<br><br>

|<img src="image/Prometheus.png" width="60"/>|<img src="image/Cortex.png" width="60" />|<img src="image/Grafana.png" width="60"/>|<img src="image/OpenSearch.png" width="57"/>|<img src="image/FluentBit.jpg" width="70"/>|<img src="image/OpenTelemetry.png" width="60"/>|<img src="image/Ansible.png" width="60"/>|
|---|---|---|---|---|---|---|
|**Prometheus**|**Cortex**|**Grafana**|**OpenSearch**|**FluentBit**|**OpenTelemetry**|**Ansible**|

## **AWSマネージドサービスの利用** ##
よく使われているOSSで監視の構築をすると、このような構成になりますが、<br>
<img src="image/Observability.drawio.png" width="400" /><br>

AWSのマネージドサービスを利用する事で、出来るだけ運用や管理が楽になるようにしています。<br>
<img src="image/Observability(AWS).drawio.png" width="400" /><br>

### Grafanaによる可視化と一元化
監視項目によってツールやサービスが分かれていると面倒なので`Grafana`のダッシュボードで出来るだけすべてを監視できるようにしました。<br>

<img src="image/Grafana.drawio.png" width="800"/><br>
`Prometheus`・`CloudWatch`・`Zabbix`からメトリクス、<br>
`OpenSearch`からログ(＋セキュリティ関連のログ)、<br>
`X-Ray`からトレース、<br>
`Athena`からコストデータとサーバー情報を可視化しています。<br><br>
本番アカウントと監視アカウントを分けると以下のようになります。<br>
<img src="image/Grafanaアカウント別.drawio.png" width="800"/><br>
オンプレで使用している`Zabbix`に関しても、バージョンが古く扱いづらいため、`Grafana`で可視化できるようにしていますが、Amazonの`Grafana`ではなく、オンプレにて別途`Grafana`を構築して使用します。<br>


## **サーバー監視**
サーバーの監視においてはobservabilityの3本の柱である、メトリクス・ログ・トレースの3つの監視を行っています。<br>
- メトリクスはPrometheus Exporterから取得したものを`OpenTelemetry`で`Prometheus`に送信し、`Grafana`で可視化してます。<br>
- ログは`Fluent Bit`からAWSのサービスである`Kinesis Data Firehose`に送信することで、`S3`にリアルタイムストリーミングを行います。<br>
`S3`から`Lambda`を利用して成形後、`OpenSearch`に送信・可視化し、`Grafana`に一元化しています。<br>
※ここで使用する`Lambda`は[SIEM on Amazon OpenSearch Service](https://github.com/aws-samples/siem-on-amazon-opensearch-service)を使用しています。<br>
- トレースは`OpenTelemetry`のSDKをアプリに導入することで、アプリから取得できるようになり、`OpenTelemetry`にて`X-Ray`用のデータに成形後、`X-Ray`に送信・可視化し、`Grafana`に一元化しています。<br>

### EC2の監視
<img src="image/EC2監視.drawio.png" width="100%"/><br>
<img src="image/Fluentdのメトリクス.drawio.png" width="30%"/><br><br>
`Fluent Bit`自体のメトリクスも`Prometheus`形式のメトリクスとして`Fluent Bit`から直接`OpenTelemetry`で収集しています。<br>
また`Fluent Bit`に`Node Exporter` `Windows Exporter`としての機能も搭載されているので、LinuxやWindowsのメトリクスも収集します。<br>

### ECSの監視
<img src="image/ECS監視.drawio.png" width="100%"/><br>
`ECS`では、`OpenTelemetry`がECSのエージェントからコンテナのメトリクスを取得します。<br>
`ECS`にてログドライバーの`FireLens`を使用しています。<br>

### EC2・ECS以外のAWSサービスの監視
<img src="image/その他AWSサービスの監視.drawio.png" width="650" /><br>
`CloudWatch`のメトリクスは監視アカウントの`Grafana`で監視してます。<br>
`CloudWatch`のログは`S3`と`OpenSearch`を経由して`Grafana`で監視してます。<br>
`Lambda`等のトレースも`X-Ray`で取得し、`CloudWatch`同様、監視アカウントの`Grafana`で監視します。<br>

## **データベース**
データベースは`PostgreSQL`と`MongoDB`を使用しています。<br><br>

|<img src="image/PostgreSQL.png" width="60" />|<img src="image/MongoDB.jpg" width="65" />|
|---|---|
**PostgreSQL**|**MongoDB**|


## **セキュリティ**
セキュリティに関しても可視化して監視しています。<br><br>
<img src="image/セキュリティ.drawio.png" width="100%"/><br>

### セキュリティログの可視化
<img src="image/セキュリティログ.drawio.png" width="800"/><br>
セキュリティログは`OpenSearch`に集約・可視化、`Grafana`に一元化しています。<br>

## **ネットワーク**

ドメインのネームサーバーをさくらインターネットから`Route53`に移管しました。<br><br>
<img src="image/ドメイン登録.drawio.png" width="300" /><br>
また、サブドメインの登録業務が多いため、ドメイン登録からレコードのバックアップ、ApacheのConfファイルの編集までを実行できるWindowsアプリを作成しました。
バックアップに関しては、AWSでは用意されていないので、こちらのアプリを使用しないと実行できません。<br>


### ネットワークの可視化
ネットワークサービスのメトリクスとログも可視化してます。<br>
<img src="image/ネットワーク.drawio.png" width="600" /><br>
ネットワークに関するメトリクスは`CloudWatch`で可視化し、`Grafana`に一元化、<br>
ネットワークに関するログは`OpenSearch`に集約・可視化、`Grafana`に一元化しています。<br>

## **コスト管理**

<img src="image/コスト管理.drawio.png" width="600" /><br>
`Budgets`と`Cost & Usage Report`のデータは`Grafana`で可視化しています。<br>

## **アカウント管理**
`IAM Identity Center`でログイン管理を行っています。<br>
<img src="image/アカウント管理.drawio.png" width="500" /><br>


## **運用の自動化**
AWSの運用は`Lambda` `EventBridge` `SNS` `SystemsManager`等使って出来るだけ自動化させてます。<br>

`EventBridge`や`SNS`をトリガーにしてLambda関数を実行しています。<br>
<img src="image/Lambda.drawio.png" width="250" /><br>

様々なサービスから`SNS`にアラートを送信してます。<br>
<img src="image/SNS.drawio.png" width="250" /><br>

`EventBridge`を利用して、`Lambda`や`SystemsManager`のAutomation、Run Commandの実行、`SNS`を利用してのメール通知を行っています。<br>
<img src="image/EventBridge.drawio.png" width="350" /><br>

CloudWatchアラームをトリガーにインスタンスを自動でスケーリングしてます。<br>
<img src="image/AutoScaling.drawio.png" width="550" /><br>

`Backup`を利用して様々なAWSサービスのバックアップのスケジュール管理やバックアップの保持期間の管理、バックアップに対するアクセスポリシーの設定を一元管理しています。<br>
<img src="image/Backup.drawio.png" width="500" /><br>

`DevOps Guru`の機械学習を利用して`CloudWatch` `Config` `CLoudFormation` `X-Ray` `Systems Manager OpsCenter`の異常を検知しています。<br>
<img src="image/DevOpsGuru.drawio.png" width="350" /><br>


誰かがルールに反した操作を行った際は、`EventBridge`か`Config`で検知し、`Systems Manager Automation`を利用して自動修復をするようにしています。<br>
<img src="image/Automation.drawio.png" width="250" /><br><br>
`Systems Manager Inventory`で取得できるサーバーのインベントリも`Glue`と`Athena`を利用して、`Grafana`で可視化をしてます。<br>
<img src="image/インベントリ.drawio.png" width="600" /><br><br>
`Grafana`だけでなく、Systems Manager ExplorerでAWSにおける様々な情報を一元化したダッシュボードを利用しています。<br>
<img src="image/Explorer.drawio.png" width="600" /><br><br>



## **構築**
AWS内のリソースは基本的に`CloudFormation(CFn)`で構築し、`CodeCommit`でバージョン管理しています。<br>
`CloudFormation(CFn)`を利用することで、AWSリソースをコードで管理できるので、現状を把握しやすくなりますし、<br>
`CodeCommit`を利用することで、変更差分がわかりやすく残るので、作業履歴として利用できます。<br>
さらに、`CodeBuild`と`CodePipline`を利用して、コミットから構築までを自動化させています。<br><br>


<img src="image/構築.drawio.png" width="500" /><br>

`Lambda`の構築・デプロイには`SAM(Serverless Application Model)`を利用しています。<br>

|<img src="image/SAM.jpg" height="75" />|
|---|
|**SAM**|

