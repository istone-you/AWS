# Grafanaのデータソースの設定(OpenSearch)
GrafanaにてデータソースにOpenSearchを設定する方法について説明します。<br><br><br>

まずはGrafanaにて`Configuration`の`Data sources`を開きます。

<img src="img/grafana.png" width="70%"/><br>

`Add data source`をクリック<br>

<img src="img/grafana2.png" width="100%"/><br>

`OpenSearch`をクリック<br>

<img src="img/grafana12.png" width="100%"/><br>

`URL`にOpenSearchのURLを入力します。<br>

<img src="img/grafana4.png" width="60%"/><br>

OpenSearchのURLはAmazon OpenSearch Serviceのマネジメントコンソール画面で確認できます。<br>

<img src="img/OpenSearch7.png" width="100%"/><br>

次に`Auth`の`SigV4 auth`をOnにして、`SigV4 Auth Details`の`Authentication Provider`を`Workspace IAM Role`にし、`Default Region`でAmazon OpenSearch Serviceのドメインを作成したリージョンを選択します。<br>

<img src="img/grafana5.png" width="80%"/><br>

`OpenSearch details`の`Index name`にOpenSearchでのインデックス名を入力します。そのため、インデックス名ごとにOpenSearchのデータソースの設定が必要になります。<br>

<img src="img/grafana13.png" width="100%"/><br>

その他の設定はそのままで、`Save & test`をクリックします。<br>
`Data source is working`と表示されれば設定完了です。<br>


<img src="img/grafana6.png" width="100%"/><br>

OpenSearchのデータをもとに、ダッシュボードを作成する際は、`Lucene Query`を使用します。<br>

<img src="img/Lucene.png" width="100%"/><br>