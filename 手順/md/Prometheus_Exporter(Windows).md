# **Windows Exporter**

Windowsのシステムメトリクスを取得するPrometheus ExporterであるWindows Exporterの導入方法を説明します。<br><br><br>

まず、[こちら](https://github.com/prometheus-community/windows_exporter/releases)から最新のバージョンのamd64.exeをサーバーにインストールしてください。<br><br>

下記コマンドで、Windows Exporterをサービス登録します。
```
C:¥>sc create "WIndows Exporter" binPath= "Windows Exporterのexeファイルのパス --collectors.enabled "[defaults],process,vmware"" start= auto
```
