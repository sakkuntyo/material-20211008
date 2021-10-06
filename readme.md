---  

theme　: "black"  
transition　: "default"  

---

## 任意の返答を返す Web アプリの作り方とその使い道
===
###### Created by dasasaki

---

## Nodejsって何

- ブラウザ(クライアントサイド)で javascript を実行できる
- Nodejs は サーバーサイド で javasciprt を実行するためのエンジン
- ブラウザに載っている javascript の実行エンジンの名前は V8 と呼ばれてます。

<!-- Note

<-->

--

### 簡単に言うと

python のコードを手元で実行するために

python をインストールする様に

javascript のコードを手元で実行するには

Nodejs をインストールすれば良いです。

---

## Nodejs インストールから Web アプリを実行するまで - デモ

--

###### Linux / Mac の場合

```bash
# node バージョンマネージャのインストール
$ git clone https://github.com/creationix/nvm ~/.nvm
## nvm コマンドを使用できる様にします。
$ source ~/.nvm/nvm.sh 
## ~/.bashrc に追記すると、プロンプトを開いた際に勝手に実行されます。
$ echo 'source ~/.nvm/nvm.sh' >> ~/.bashrc　

# node のインストール
$ nvm install --lts # windowsの場合は--lts を latest に置き換えます。
		
# express フレームワークのジェネレータをインストール
$ npm install -g express-generator
		
# アプリ作成
$ express testProject
$ cd testProject
$ ll ./ # このフレームワークのひな型が出来ている事がわかります。
		
# アプリ起動
$ npm i
$ npm start 
# Webアプリが起動されます ポート番号はデフォルトで3000
```

<!-- Note
3000 ポート番号が書いてある bin/www についても言及
<-->

--

###### Windows の場合

```cmd
# node バージョンマネージャのインストール
# 以下から インストーラ (nvm-setup.zip) を入手して、インストールします。
https://github.com/coreybutler/nvm-windows/releases

# node のインストール
$ nvm install latest
		
# express フレームワークのジェネレータをインストール
$ npm install -g express-generator
		
# アプリ作成
$ express testProject
$ cd testProject
$ ls ./ # このフレームワークのひな型が出来ている事がわかります。
		
# アプリ起動
$ npm i
$ npm start 
# express フレームワーク で構築された Webサーバーが起動します。
ポート番号はデフォルトで3000
```

--

##### この Web サーバーアプリを起動し、アクセスしてみる

```bash
$ npm start 
```

上記を実行した状態で

ブラウザでサーバーにアクセスしてみます。

↓の様な画面が出れば ok

URL : localhost:3000

![20211007062401](https://user-images.githubusercontent.com/20591351/136286459-664a202a-6dd6-4320-8a88-4a8402be5930.jpg)

---

##### レスポンス内容を変更する - デモ

localhost:3000 にアクセスして表示されるページは

以下のファイルで決められています。

- routes/index.js
```js
var express = require('express');
var router = express.Router();

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;
```

以下の部分ですね。

```
res.render('index', { title: 'Express' });
```

--

##### res.render の意味

```
res.render('index', { title: 'Express' });
```

は

views/index.jade に引数 title を与えて

html化したものを返答するよ、という意味

なので index.jade を変更しても良いですが、

今回は任意の json を返答する様にします。

--

###### 任意の json を返答してみる

```
res.render('index', { title: 'Express' });
```

を 以下の様に変更します。

```
res.json({keyname:"value"});
```

もう一度ウェブサーバーを起動してアクセスすると、以下の様に返答されます。

![image](https://user-images.githubusercontent.com/20591351/136287974-c8a21d5a-c59d-4364-b3c8-8dddfa1fd245.png)

--

###### 任意の json (リクエストヘッダ) を返答してみる - 1
　
```
router.get('/', function(req, res, next) {
```

の req には

リクエストに関する情報が含まれています。

以下の行を追加して、コンソールに出力すると

色々情報が入っている事がわかります。

```
console.dir(req)
```

ここに含まれるリクエストヘッダを返答させます

--

###### 任意の json (リクエストヘッダ) を返答してみる - 2

```
res.render('index', { title: 'Express' });
```

を以下の様に変更してみます。

```
res.json(req.headers)
```

するとこの様な返答が帰ってきます。

![image](https://user-images.githubusercontent.com/20591351/136288883-73fe5a7d-5389-4fd2-ac4d-7fe867dc591f.png)

--

###### 任意の json (リクエストヘッダ) を返答してみる - 3

このサーバーがくれたリクエストヘッダ

```json
{
  "host": "localhost:3000",
  "connection": "keep-alive",
  "cache-control": "max-age=0",
  "sec-ch-ua": "\"Chromium\";v=\"94\", \"Microsoft Edge\";v=\"94\", \";Not A Brand\";v=\"99\"",
  "sec-ch-ua-mobile": "?0",
  "sec-ch-ua-platform": "\"Windows\"",
  "upgrade-insecure-requests": "1",
  "user-agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/94.0.4606.71 Safari/537.36 Edg/94.0.992.38",
  "accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9",
  "sec-fetch-site": "none",
  "sec-fetch-mode": "navigate",
  "sec-fetch-user": "?1",
  "sec-fetch-dest": "document",
  "accept-encoding": "gzip, deflate, br",
  "accept-language": "ja",
  "if-none-match": "W/\"13-QmJyzBdpUWUhu6StMCN54DSU6fY\""
}
```

--

###### 任意のファイルを返答してみる

routes に 適当な html を配置しておきます。

test.html
```html
<html>
    <body>
        hello team
    </body>
</html>
```

--

###### 任意のファイルを返答してみる - html

```
res.render('index', { title: 'Express' });
```

の部分を以下に変えてみます。

```
res.sendFile(__dirname + '/' +'test.html');
```

この様な表示になります。

![image](https://user-images.githubusercontent.com/20591351/136292358-704b1468-a005-4a4a-81e5-6edee1c3a8b6.png)

--

###### 任意のファイルを返答してみる - mp3

もしこの html が mp3 だった場合、また少しブラウザ側の挙動が変わります。

```
res.render('index', { title: 'Express' });
```

の部分を以下に変えてみます。

```
res.sendFile(__dirname + '/' +'test.mp3');
```

![image](https://user-images.githubusercontent.com/20591351/136292862-29afab36-a8e2-467e-a2b9-bb86f1d29d6e.png)

--

###### リダイレクトさせてみる

```
res.render('index', { title: 'Express' });
```

の部分を以下に変えてみます。

```
res.redirect(302, 'https://microsoft.com')
```

![image](https://user-images.githubusercontent.com/20591351/136293654-c31b944f-06f0-4f01-9053-35274630f908.png)

---

###### 蛇足

この資料は reveal.js で作りました。

- 公式
https://revealjs.com/

- 以下がわかりやすかったです。
https://jyun76.github.io/revealjs-vscode/









