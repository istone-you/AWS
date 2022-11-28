# **監視の構築手順**
- [メトリクスの取得手順](#メトリクスの取得手順)
- [ログの取得手順](#ログの取得手順)
- [トレースの取得手順](#トレースの取得手順)
- [セキュリティログの取得手順](#セキュリティログの取得手順)
- [Athenaの設定](#athenaの設定)
- [Grafanaの設定](#grafanaの設定)

## **メトリクスの取得手順**
メトリクスの取得から可視化までの手順です。<br>
流れとしては以下の画像の通りです。<br><br>
<img src="img/メトリクス.drawio.png" width="100%"/><br>

それぞれの導入方法と設定方法についての手順を説明いたします。<br><br><br>
**①[Prometheus Exporter (Linux)](md/Prometheus_Exporter(Linux).md)** / **[Prometheus Exporter (Windows)](md/Prometheus_Exporter(Windows).md)**<br><br>
**②[AWS Distro for OpenTelemetry (Linux)](md/ADOT(Linux).md)** / **[AWS Distro for OpenTelemetry (Windows)](md/ADOT(Windows).md)** / **[AWS Distro for OpenTelemetry (ECS)](md/ADOT(ECS).md)**<br><br>
**③[IAM Role](md/IAM(AMP).md)** <br><br>
**④[Amazon Managed Service for Prometheus](md/AMP.md)**<br><br>
**⑤[Amazon Managed Grafana](#grafanaの設定)**<br><br>

<a href="img/Grafanaアラート(EC2)メトリクス.drawio.png" target="_blank"><img src="img/Grafanaアラート(EC2)メトリクス.drawio.png" width="100%"/></a><br>
※クリックで画像表示<br>

## **ログの取得手順**
ログの取得から可視化までの手順です。<br>
流れとしては以下の画像の通りです。<br><br>
<img src="img/ログ.drawio.png" width="100%"/><br>

それぞれの導入方法と設定方法についての手順を説明いたします。<br><br><br>
**①[Fluent Bit(Linux)](md/FluentBit(Linux).md)** / **[Fluent Bit(Windows)](md/FluentBit(Windows).md)** / **[Fluent Bit(ECS)](md/FluentBit(ECS).md)**<br><br>
**②[IAM Role](md/IAM(S3).md)**<br><br>
**③[SIEM on OpenSearch Serviceのインストール](md/SIEM.md)**<br><br>
**④[SIEM on OpenSearch Serviceの設定](md/SIEM(設定).md)**<br><br>
**⑤[Amazon Managed Grafana](#grafanaの設定)**<br><br>

<a href="img/Grafanaアラート(EC2)ログ.drawio.png" target="_blank"><img src="img/Grafanaアラート(EC2)ログ.drawio.png" width="100%"/></a><br>
※クリックで画像表示<br>

## **トレースの取得手順**
トレースの取得から可視化までの手順です。<br>
流れとしては以下の画像の通りです。<br><br>
<img src="img/トレース.drawio.png" width="100%"/><br>

それぞれの導入方法と設定方法についての手順を説明いたします。<br><br><br>
**①[OpenTelemtry SDK(C#)](md/OtelSDK(C#).md)** / **[OpenTelemtry SDK(PHP)](md/OtelSDK(PHP).md)**<br><br>
**②[AWS Distro for OpenTelemetry](md/ADOT(トレース).md)**<br><br>
**③[Amazon Managed Grafana](#grafanaの設定)**<br><br>

<a href="img/Grafanaアラート(EC2)トレース.drawio.png" target="_blank"><img src="img/Grafanaアラート(EC2)トレース.drawio.png" width="100%"/></a><br>
※クリックで画像表示<br>

## **セキュリティログの取得手順**
セキュリティログの取得から可視化までの手順です。<br>
流れとしては以下の画像の通りです。<br><br>
<img src="img/セキュリティログ.drawio.png" width="100%"/><br>

それぞれの導入方法と設定方法についての手順を説明いたします。<br><br><br>
**①[セキュリティサービスのログの取り込み設定](md/SIEM(セキュリティ).md)**<br><br>
**②[Amazon Managed Grafana](#grafanaの設定)**

## Athenaの設定
コストデータとサーバーのインベントリデータをGlueでテーブル作成し、Athenaでのクエリ出来るようにするまでの手順です。<br>
流れとしては以下の画像の通りです。<br><br>
<img src="img/Athena.drawio.png" width="100%"/><br>

それぞれの導入方法と設定方法についての手順を説明いたします。<br><br><br>
**①[Cost and Usage Report](md/CUR.md)**<br><br>
**②[Systems Manager Inventory](md/Inventry.md)**<br><br>
**③[Amazon Managed Grafana](#grafanaの設定)**

## **Grafanaの設定**
<img src="img/Grafana.drawio.png" width="100%"/><br>

Grafanaの導入方法と設定方法についての手順を説明いたします。<br><br><br>
**①[Amazon Managed Grafanaの作成](md/AMG.md)**<br><br>
**②[データソースの設定(Prometheus)](md/AMG(AMP).md)**<br><br>
**③[データソースの設定(CloudWatch)](md/AMG(CW).md)**<br><br>
**④[データソースの設定(X-Ray)](md/AMG(XRay).md)**<br><br>
**⑤[データソースの設定(OpenSearch)](md/AMG(OS).md)**<br><br>
**⑥[データソースの設定(Athena)](md/AMG(Athena).md)**<br><br>

## **アラートの設定**
Grafanaにてメトリクスやログのアラートを発報し、メールを送信したりコマンドを実行する手順です。<br>
流れとしては以下の画像の通りです。<br><br>
<img src="img/Grafanaアラート.drawio.png" width="70%"/><br>
それぞれの導入方法と設定方法についての手順を説明いたします。<br><br><br>

**①[Amazon Managed Grafanaでのアラート設定](md/AMGアラート.md)**<br><br>
**②[SNS](md/SNS.md)**<br><br>
**③[Lambda](md/Lambda.md)**<br><br>
