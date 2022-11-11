# **AWS Distro for OpenTelemetry (トレース)**
AWS Distro for OpenTelemetry(ADOT)を導入する手順については **[AWS Distro for OpenTelemetry (Linux)](ADOT(Linux).md)**か**[AWS Distro for OpenTelemetry (Windows)](ADOT(Windows).md)** 参照してください。<br><br>
今回はADOTでのトレースの設定手順を説明します。
下記コマンドでADOTの設定ファイルを編集します。
```
# vi /etc/amazon/aws-otel-collector/config.yaml
```
下記内容を追加してください。
```diff
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
        - targets: [ localhost:9100 ] # Node Exporter
        - targets: [ localhost:9117 ] # Apache Exporter
+  otlp:
+    protocols:
+      grpc:
+        endpoint: 0.0.0.0:4317
+      http:
+        endpoint: 0.0.0.0:4318
exporters:
  prometheusremotewrite:
    endpoint: # AWS Managed Prometheus(AMP)のエンドポイント
    auth:
      authenticator: sigv4auth
    resource_to_telemetry_conversion:
      enabled: true
+  awsxray:
+    region: "ap-northeast-1"
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
    metrics/ecs:
      receivers: [awsecscontainermetrics]
      exporters: [prometheusremotewrite]
+    traces:
+      receivers: [otlp]
+      exporters: [awsxray]
```
設定が完了したら、ADOTを再起動し、
```
# systemctl restart aws-otel-collector
```
起動しているかどうか確認してください。
```
# systemctl status aws-otel-collector
```
起動していればADOTでのトレースの準備は完了です。