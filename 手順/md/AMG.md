# **Amazon Managed Grafana**
GrafanaのAWSマネージドサービスである、Amazon Managed Grafana(AMG)の導入方法を説明します。<br><br><br>
まずAWSのマネジメントコンソールにてAMGを開いて、作成をクリックします。<br>

<img src="img/AMG.png" width="100%"/><br>

ワークスペース名に作成するAMGの名前を入力し、次へをクリックします。<br>

<img src="img/AMG作成.png" width="100%"/><br>

認証アクセスのAWS IAM IDセンターにチェックを入れて、次へをクリックします。<br>
※IAM Identity Centerの作成については[こちら](SSO.md)を参照。

<img src="img/認証アクセス.png" width="100%"/><br>

サービスマネージド型のアクセス許可設定で、

<img src="img/アクセス許可.png" width="100%"/><br>

Grafanaのデータソースとして使用したいサービスを選択して、次へをクリック<br>

<img src="img/データソース選択.png" width="100%"/><br>

確認画面になるので、間違いなければワークスペースを作成をクリック<br>

<img src="img/AMG確認.png" width="100%"/><br>

作成中になっているステータスが、

<img src="img/AMG作成中.png" width="100%"/><br>

アクティブになれば作成完了です。
<img src="img/AMG作成完了.png" width="100%"/><br>