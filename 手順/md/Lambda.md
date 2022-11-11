# Lambdaの設定
LambdaにてSystems ManagerのRunCommandを実行する方法について説明します。<br><br><br>


`index.js`に下記コードを記載します。<br>
```js
'use strict';

const AWS = require('aws-sdk')
const ssm = new (require('aws-sdk/clients/ssm'))();

module.exports.handler = async event => {

  try {
    let params = {
      DocumentName: 'AWS-RunShellScript',
      InstanceIds: ['i-0a8902b63ea520516'], //コマンドを実行するインスタンスID
      Parameters: {
        commands: ['mkdir test', 'touch test.txt'], // EC2で実行したいコマンド(複数可)
        workingDirectory: ['/tmp'] // コマンドを実行するディレクトリ
      },
      TimeoutSeconds: 3600 // タイムアウト設定(秒)
    }
    const sendCommandResult = await ssm.sendCommand(params).promise();

  } catch(e){
    console.log(e);
  }

};
```