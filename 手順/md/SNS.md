# SNS
Amazon SNSの設定方法について説明します。<br><br><br>

AWSマネジメントコンソールからSNSを開き、トピックを選択します。<br>

<img src="img/sns.png" width="30%"/><br>

`トピックの作成`をクリックします。<br>

<img src="img/sns3.png" width="100%"/><br>

タイプは`スタンダード`を選択し、適当な名前と表示名を入力し、一番下の`トピックの作成`をクリックします。<br>

<img src="img/sns2.png" width="100%"/><br>

トピックを作成したら、トピックを開き`サブスクリプションの作成`をクリックします。<br>

<img src="img/sns4.png" width="100%"/><br>

プロトコルで`AWS Lambda`もしくは`Eメール`を選択します。

<img src="img/sns5.png" width="100%"/><br>

Eメールの場合はエンドポイントにEメールアドレスを、<br>

<img src="img/sns6.png" width="100%"/><br>

Lambdaの場合はエンドポイントにLambda関数のArnを入力します。Lambda関数の作成については[こちら](Lambda.md)です。

<img src="img/sns7.png" width="100%"/><br>

入力したら、右下の`サブスクリプションの作成`をクリックして完了です。<br>

Eメールの場合は承認が必要なので、サブスクリプションの欄から`保留中の確認`になっているものを選択し、`サブスクリプションの確認`をクリックします。
<img src="img/sns8.png" width="100%"/><br>

メールが届いていると思うので、届いたメールに`Confirm subscription`と記載されているリンクのURLをコピーして、`サブスクリプション確認URL`の欄に貼り付けます。`サブスクリプションの確認`をクリックして完了です。<br>
<img src="img/sns9.png" width="50%"/><br>

