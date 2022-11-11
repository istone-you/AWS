# Systems Manager Inventory

サーバー内のメタデータを取得するSystems Manager Inventoryの設定方法について説明します。<br><br><br>

まずAWSマネジメントコンソールでSystems Managerの`インベントリ`を開き、`リソースデータの同期`をクリックします。<br>

<img src="img/ssminventory.png" width="100%"/><br>

`リソースデータの同期の作成`をクリック。

<img src="img/ssminventory2.png" width="100%"/><br>

同期名は適当な名前を入力し、データを保存するS3バケットとバケット上でのフォルダパス、バケットのリージョンを設定。KMSは別途作成したKMSのArnを入力して作成をクリックしてください。

<img src="img/ssminventory3.png" width="100%"/><br>

これで、サーバーのインベントリデータがS3に出力され、Athenaで確認できるようになります。<br>