# **Node Exporter** #

Linuxのシステムメトリクスを取得するPrometheus ExporterであるNode Exporterの導入方法を説明します。<br><br><br>

まず、Node Exporterをインストールしたいディレクトリに移動して下記コマンドを実行し、インストールと解凍を行います。
```
$ wget https://github.com/prometheus/node_exporter/releases/download/v1.1.2/node_exporter-1.1.2.linux-amd64.tar.gz
$ tar xfz node_exporter-1.1.2.linux-amd64.tar.gz
```
今回はバージョン1.1.2を使用していますが、[こちら](https://github.com/prometheus/node_exporter/releases)から最新のバージョンのものをインストールしてください。<br>

次に、Node ExporterをSystemdに登録します。<br><br><br>
Node Exporter用のファイルを作成し、
```
# vi /etc/systemd/system/node_exporter.service
```
下記のように記載してください。
```
[Unit]
Description=node exporter
After=syslog.target network.target

[Service]
Type=simple

ExecStart=/usr/local/node_exporter-1.2.2.linux-amd64/node_exporter　--collector.interrupts --collector.logind --collector.mountstats --collector.systemd --collector.textfile.directory=/etc/prometheus/textfile
ExecStop=/bin/kill -INT $MAINPID

[Install]
WantedBy=multi-user.target
```
Node Exporterを起動し、
```
# systemctl start node_exporter.service
```
起動しているかどうか確認してください。
```
# systemctl status node_exporter.service
```

起動していればNode Exporterの準備は完了です。<br><br><br>

## collector.textfile
Node Exporterで取得できないメトリクスに関しては、Node Exporterの機能であるcollector.textfileを利用します。<br>
コマンドの実行結果をテキストファイル(.prom)に出力し、その結果をメトリクスとして参照します。<br>
テキストファイルの場所は起動コマンドのオプションである`--collector.textfile.directory`で指定しており、今回は`/etc/prometheus/textfile`としています。
