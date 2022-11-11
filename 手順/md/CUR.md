# Cost And Usage Reports
Cost And Usage ReportsをAthenaで確認する設定方法です。<br><br><br>

まずはAWSマネジメントコンソールの右上から、請求ダッシュボードを開きます<br>

<img src="img/cur.png" width="30%"/><br>

`Cost & usage reports`をクリックします。

<img src="img/cur2.png" width="20%"/><br>

`レポートの作成`をクリックします。<br>

<img src="img/cur3.png" width="100%"/><br>

適当に名前を決め、次へをクリック<br>

<img src="img/cur4.png" width="100%"/><br>

レポートを保存するS3バケットとバケットでのパス、使用料の時間詳細度を設定し、
レポートデータ統合の有効化を`Amazon Athena`に設定します。<br>
あとは次へをクリックしてそのままレポートを作成してください。<br>

<img src="img/cur5.png" width="100%"/><br>

翌日にCost And Usage Reportsのレポートが作成され、コスト情報をAthenaで確認できるようになります。<br>