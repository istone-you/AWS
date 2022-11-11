# Grafanaのデータソースの設定(Athena)
GrafanaにてデータソースにAthenaを設定する方法について説明します。<br><br><br>

まずはGrafanaにて`Configuration`の`Data sources`を開きます。

<img src="img/grafana.png" width="70%"/><br>

`Add data source`をクリック<br>

<img src="img/grafana2.png" width="100%"/><br>

`Amazon Athena`をクリック<br>

<img src="img/grafana10.png" width="100%"/><br>

`Authentication Provider`を`Access & secret key`にして、本番アカウントのユーザーの`Access Key`と`Secret Access Key`を入力します。ここでは`AmazonAthenaFullAccess`の権限を持ったユーザーのアクセスキーを使用しています。（権限は絞ってください。）

<img src="img/grafana8.png" width="80%"/><br>

その他の設定はそのままで、`Save & test`をクリックします。<br>
`Data source is working`と表示されれば設定完了です。<br>


<img src="img/grafana6.png" width="100%"/><br>