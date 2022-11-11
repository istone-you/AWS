# Grafanaのデータソースの設定(Prometheus)
GrafanaにてデータソースにPrometheusを設定する方法について説明します。<br><br><br>

まずはGrafanaにて`Configuration`の`Data sources`を開きます。

<img src="img/grafana.png" width="70%"/><br>

`Add data source`をクリック<br>

<img src="img/grafana2.png" width="100%"/><br>

`Prometheus`をクリック<br>

<img src="img/grafana3.png" width="100%"/><br>

`URL`にAMPのURLを入力します。<br>

<img src="img/grafana4.png" width="60%"/><br>

AMPのURLはAMPのマネジメントコンソール画面で確認できます。こちらのURLから末尾の`/api/v1/remote_write`を省いたものがAMPのURLです。<br>

<img src="img/amp-endpoint.png" width="100%"/><br><br>

次に`Auth`の`SigV4 auth`をOnにして、`SigV4 Auth Details`の`Authentication Provider`を`Workspace IAM Role`にし、`Default Region`でAMPのワークスペースを作成したリージョンを選択します。<br>

<img src="img/grafana5.png" width="80%"/><br>

その他の設定はそのままで、`Save & test`をクリックします。<br>
`Data source is working`と表示されれば設定完了です。<br>


<img src="img/grafana6.png" width="100%"/><br>

Prometheusのデータをもとに、ダッシュボードを作成する際は、`PromQL`というPrometheus用のクエリを使用します。<br>

<img src="img/PromQL.png" width="100%"/><br>










