# **Windows Exporter**

Windowsのシステムメトリクスを取得するPrometheus ExporterであるWindows Exporterの導入方法を説明します。<br><br><br>

まず、[こちら](https://github.com/prometheus-community/windows_exporter/releases)から最新のバージョンのamd64.exeをサーバーにインストールしてください。<br><br>

下記コマンドで、Windows Exporterをサービス登録します。
```
C:¥>sc create "Windows Exporter" binPath=  "C:\Users\Administrator\Desktop\windows_exporter-0.20.0-amd64.exe --collectors.enabled [defaults],process,scheduled_task,vmware --collector.service.services-where=\"Name='サービス名' OR Name='サービス名'\" --collector.process.whitelist=\"(プロセス名^|プロセス名).*\" --collector.scheduled_task.whitelist=\"(タスクのパス^|タスクのパス).*\"start= auto
```
例）
```
C:¥>sc create "Windows Exporter" binPath=  "C:\Users\Administrator\Desktop\windows_exporter-0.20.0-amd64.exe --collectors.enabled [defaults],process,scheduled_task,vmware --collector.service.services-where=\"Name='windows_exporter' OR Name='fluent-bit' OR Name='awsotelcollector'\" --collector.process.whitelist=\"(windows_exporter-0.20.0-amd64^|fluent-bit^|.aws-otel-collector).*\" --collector.scheduled_task.whitelist=\"(/Microsoft/Windows/Location/Notifications^|/Microsoft/Windows/PushToInstall/LoginCheck).*\"start= auto
```
