# **AWS Distro for OpenTelemetry (Linux)**
AWS Distro for OpenTelemetry(ADOT)をLinuxに導入する手順について説明します。<br><br><br>
まずAWSのマネジメントコンソールにてSystems Managerを開いて、左下のディストリビューターを選択してください。<br>

<img src="img/ディストリビューター.png" width="100%"/><br><br>

AWSDistroOtel-Collectorを選択します。<br>

<img src="img/パッケージ.png" width="100%"/><br>

一回限りのインストールをクリックします。<br>
<img src="img/インストール.png" width="100%"/><br>

コマンドを実行の画面になったら、ターゲットからインストールしたいインスタンスを選択してください。<br>
他の設定はデフォルトのままで大丈夫です。<br>
選択したら画面一番下にある実行をクリックします。<br>

<img src="img/ターゲット.png" width="100%"/><br>

実行すると、ステータスが進行中になります。<br>

<img src="img/進行中.png" width="100%"/><br>

ステータスが進行中から成功に変われば完了です。<br>

<img src="img/成功.png" width="100%"/><br><br>

成功したらサーバーにログインし、`/etc/amazon/`に`aws-otel-collector`があるかどうか確認してください。
```
# cd /etc/amazon/
# ls
aws-otel-collector
```
`aws-otel-collector`ディレクトリの中にある`config.yaml`がADOTの設定ファイルです。
```
# cd aws-otel-collector/
# ls
config.yaml  extracfg.txt
```
`config.yaml`を編集します。
```
# vi config.yaml
```

以下の様に設定ファイルを編集します。<br>
```yaml
receivers:
  prometheus:
    config:
      global:
        scrape_interval: 1m
        scrape_timeout: 10s
      scrape_configs:
      - job_name: "prometheus"
        static_configs:
        # Exporterのポート番号を下記のように記載
        - targets: [ localhost:2021 ] # Fluent Bit
          labels:
            server: 'solamame'        # サーバー名
        - targets: [ localhost:9100 ] # Node Exporter
          labels:
            server: 'solamame'        # サーバー名
        - targets: [ localhost:9117 ] # Apache Exporter
          labels:
            server: 'solamame'        # サーバー名
processors:
  filter:
    metrics:
      include:
        match_type: strict
        metric_names:
          - 取得したいメトリクス
          -         〃
          -         〃
          -         〃
exporters:
  prometheusremotewrite:
    endpoint: # AWS Managed Prometheus(AMP)のエンドポイント - リモート書き込み URL
    auth:
      authenticator: sigv4auth
    resource_to_telemetry_conversion:
      enabled: true
extensions:
  health_check:
  pprof:
    endpoint: :1888
  zpages:
    endpoint: :55679
  sigv4auth:
    service: "aps"
    region: "ap-northeast-1"
    assume_role:
      arn: # 他アカウントのAMPを使用する権限を持ったIAMロールのARN
      sts_region: "ap-northeast-1"
service:
  extensions: [pprof, zpages, health_check, sigv4auth]
  pipelines:
    metrics:
      receivers: [awsecscontainermetrics]
      processors: [filter]
      exporters: [prometheusremotewrite]
```
AMPの作成については[こちら](AMP.md)、IAMロールの作成については[こちら](IAM(AMP).md)を確認してください。<br>
AMPのリモート書き込みURLはAMPのマネジメントコンソール画面で確認できます。<br>
<img src="img/amp-endpoint.png" width="100%"/><br><br>
設定が完了したら、ADOTを起動し、
```
# systemctl start aws-otel-collector
```
起動しているかどうか確認してください。
```
# systemctl status aws-otel-collector
```
起動していればADOTの準備は完了です。

