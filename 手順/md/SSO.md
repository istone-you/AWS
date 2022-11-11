# IAM Identity Center
Amazon Managed Grafanaを使用するために、IAM Identity Centerの設定を行います。<br>
IAM Identity Centerを使用すると、複数のAWSアカウントやOpenSearchへのログインも行えます。<br><br><br>

AWSのマネジメントコンソールからIAM Identity Centerを開き、`有効にする`をクリックします。<br>

<img src="img/sso.png" width="100%"/><br>

`ユーザーを追加`をクリックします。<br>

<img src="img/sso3.png" width="100%"/><br>

情報を入力してユーザーを作成してください。<br>

<img src="img/sso4.png" width="70%"/><br>

Amazon Managed Grafanaを利用するにはAmazon Managed Grafanaにて別途設定が必要です。マネジメントコンソールでワークスペースを開いて、`ユーザーとユーザーグループの設定`をクリックします。<br>

<img src="img/sso2.png" width="100%"/><br>

先ほど作成したユーザーを割り当ててください。もしくはグループを作成してグループを割り当ててください。<br>

<img src="img/sso5.png" width="100%"/><br>


