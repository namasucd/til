フォーマット
# 結論(タイトル)
## 現象(エラーなど)
## 理由
## 原因
## 対策
## その他
- linkなど

<details><summary>

# GitHubの認証をSSHに変えたら、git remote set-urlでリモートリポジトリのURLを修正すること

</summary><div>

## HTTPS接続からSSH接続に変更したのに、push時にUsernameとPasswordを求められた
- リモートリポジトリのurlがhttps://github.com/githubユーザ名/githubリポジトリ名.git
  - git remote -v で確認する
## URLを変更する
- '~~https://github.com/~~' → 'git@github.com'
- git remote set-url repositoryname git@github.com:ユーザ名/リポジトリ名.git
## 参考
- [GitHubでssh接続する手順~公開鍵・秘密鍵の生成から~ - Qiita](https://qiita.com/shizuma/items/2b2f873a0034839e47ce)
- [githubを二段階認証に変更後に起こるエラーの対処方法 - Qiita](https://qiita.com/sayama0402/items/670b6b650ebdd8680a0b)
</div></details>

<details><summary>

# プログラム開発の基本的な考え方

</summary><div>

## フォルダ=プログラムの機能の単位
- NPMなど、パッケージと呼ばれるものは、ソースファイルをまとめたフォルダを単位として機能を提供する。
- package.jsonがその証左
- javaならば plugin.xmlでまとめたプロジェクトか。
## フォルダ=管理の単位
- ルートディレクトリに`git init`することで、フォルダの変更履歴をまとめて管理できる
- 同様に`code .`でまとまった編集を行う
</div></details>

<details><summary>

# WSLでファイル操作するためにwsl.confでoption=metadataを指定する

</summary><div>

## 通常、WSLからはマウントしたファイルを操作できない?
1. WSL(Linux)ではファイルの読み込み(r)、書き込み(w)、実行(x)の3つをまとめてパーミッションと呼び、ファイルにmetadataとして関連付けて管理している
    - cf) パーミッション関連のコマンド
      - `ls`: 先頭にファイルのパーミッションが表示される(rwxrwxrwx ファイル)
      - `cmod`: ファイルのパーミッション変更
      - `umask`: 初期パーミッションの設定。引数なしで確認可
    - パーミッションの読み方
      - **755**: 数字は 「1-読み込み可」「2-書き込み可」「4-実行可」の足し算。 並びは対象。順に所有者、グループ、その他
      - **rwxr-xr-x**: read write x(実行)。3桁区切りで対象
1. WSLは「VolFs」と「DrvFs」という2つのファイルシステムを合わせて運用している
    - VolFs: WSL内部を管理
    - DrvFs: WSL外部を管理
1. DrvFsにはこのパーミッションの設定を持ち込めず、ウィンドウズ側のパーミッション(一般に「ファイルへのアクセス権」。パーミッションはLinux用語)が適用されるが、ここで基本的にはじかれるらしい。
1. マウント時にmetadetaオプションをつけてパーミッションの設定を保存できるようにする必要がある
## 結局どうすればいいのか
- /etc/wsl.confに設定を書き込む。/etc/wsl.confがなければ作って書き込む。

```
$ sudo tee /etc/wsl.conf << EOF >/dev/null
[automount]
options = "metadata"
EOF
```
- windowsとwslを再起動する
## 参考
- [WSL でマウントしたファイルシステムでもパーミッションを扱えるようにする - らくがきちょう](https://sig9.hatenablog.com/entry/2020/02/19/000000)
- [umaskコマンドについて詳しくまとめました 【Linuxコマンド集】](https://eng-entrance.com/linux-command-umask)
- [デフォルトのパーミッション設定： umask | 知識の箱](http://www.rivhiro-weather.com/knowledge/?p=168)
</div></details>

<details><summary>

# 直前のコミットを修正するgit commit --amend

</summary><div>

そのまんま。
## 使いどころ
- 一部のファイルをaddし忘れたとき
- コミットメッセージを変更したいとき
## ※リモートにpushした後に--amendしてはいけない!!
リモートとの整合性が失われてしまうため
## 参考
- [git commit --amend で，ちょっとしたミスをしれっと直す - すこしふしぎ．](http://ism1000ch.hatenablog.com/entry/2014/03/26/190939)
</div></details>

<details><summary>

# Billboard Deviceとは、接続先のデバイスを正しく表示できなかった際の名前

</summary><div>

## USB Type-Cで追加された機能に関係したもの
- USB Type-C(端子の規格) + USB3.1(通信の規格)で新しく追加された機能「USB PD」「Alternate Mode」
- 「USB PD」 : 電源の規格。電源通信用の通信線を設けることで、最適化された電力のやり取りが可能になった
- 「Aleternate Mode」 : USB Type-Cで追加された通信線を**USB以外の通信に使ってよい**という機能。これを利用することで、USB(Type-C)を映像出力等に利用できるようになった
## 本題
- Billboard Deviceは、接続先のデバイスが「Alternate Mode」に対応していないときに接続先となり、その旨を通知する機能を持つ
- 「Aleternate Mode」に対応しているのは現状「DesignPort」「Thunderbolt3」くらい
## ではなぜUSB Type-C ←→ HDMI の変換ケーブルが売られているのか、そしてちゃんと機能するのか
宿題
## 調べた背景
1. HP ENVY x360をモニタにつないだらスリープから復帰しなくなるエラーが起きるようになった。  
1. 通知領域を見ると「Billboard Device」なる見たことのないデバイスがつながっている。
1. こいつが怪しい。
1. デバイスマネージャでモニタとして検出されていたデバイスのドライバを見ると以下の2つ。
    - 汎用PnPモニター
    - AMD High Definition Audio Device
1. 下はいらんやろ。余計なことしてそうだし。無効化！
1. なおった。
1. 「2. 3.」の過程はいらなかったけどせっかくだからまとめておこう。
## 参考
- [Type-Cデバイスのユーザー通知 ─ Billboardとは？ - Technical Direct](http://www.technical-direct.com/jp/typec_billboard/)
- [USB Type-Cの機能やオルタネートモードを「わかりやすく」解説してみる - シュウジマブログ](https://www.shujima.work/entry/2018/12/08/011441)  
## その後
- スリープ後はシャットダウン。復帰しなくなるよりはましだが。。。  
- 上述のAMD High Definition Audio Deviceが表示されなくなった。
- Bluetoothとの相性も悪し。
</div></details>

<details><summary>

# 見出し1の大きさのアコーディオンをつくりたい

</summary><div>

## サンプル
- aaa
  - bbb
1. 1
1. 2
  1. 2-1
  1. 2-2
1. 3

``` cmd
$ echo コマンドとか
```
---
## 書き方

\<details>\<summary>

\# ↑↓一行開けなければいけない。そもそも▼が上にずれる

\</summary>\<div>

\## 

\</div>\</details>
***
- 「素早く構造化した文章がかける」というmarkdownの長所丸つぶれ
- 長々と下に続けるべきではない?
  - 目次を作るのは一つの手
- __tilの整理術__
</div></details>



<details><summary>

# VSCodeで言語のスニペットを登録する
</summary><div>

ここではmarkdownにアコーディオン作成のスニペット追加する手順を例にする
## markdown.jsonの作成
- ファイル→ユーザー設定→ユーザースニペット→markdownで検索
- markdown.jsonが開かれるので、以下の記述を追加する。
```json
"accordion": {                    #スニペット名
  "prefix": "accordion",          #prefix 呼び出すための文字列
  "body": ["<details><summary>",  #body 呼び出される文字列
  "",                             #レイアウト崩れ防止のための空行。任意。
  "$1",                           #'$1'を記述した場所にカーソル位置を指定可能
  "</summary><div>",
  "",                             #レイアウト崩れ防止のための空行。ほぼ必須
  "$2",                           #カーソル位置は複数指定可能。Tabキーで次へ移動
  "</div></details>",
  ]
},                                #複数登録はカンマ区切りで。jsonだもの
```

## settings.jsonの編集
デフォルトではmarkdownファイルの編集時にスニペットが使えない設定になっているため、  
settings.jsonに以下の記述を追加する
```json
"[markdown]": {
    "editor.wordWrap": "on",            #折り返しをするか。デフォルトでon
    "editor.quickSuggestions": true,    #ここがデフォルトでfalseになっているためスニペットが使えない
    "editor.snippetSuggestions": "top", #お好み。スニペットが候補ボックスのどこに表示されるか。多分不要
    "editor.suggest.showWords": false,  #お好み。falseにしないとファイル内のテキストを勝手に拾ってきてサジェストしてくるのでうっとうしい
},
```
- settings.jsonで"markdown"と入力するといい感じに補完してくれる
</div></details>

<details><summary>

# windowsのカーネルではシングルクォーテーションが使えないのでエスケープが必要
</summary><div>

## powershellからcurlコマンドでJSONデータをポストしようとしたところ、下記エラーメッセージが返された。JSONの形式がおかしいらしい

> message": "Bad credentials", "documentation_url": "https://docs.github.com/rest"

## 対策  
エスケープ文字を使いダブルクォーテーションを記述する
``` powershell
-d '"{\"query\": \"hogehoge\"}"'
```  

## 失敗した対策
- `ConvertTo-Json` コマンドで作成したJSONオブジェクトをPOSTする
``` powershell
 -d (@{"query"="hogehoge"} | ConvertTo-Json) 
```

- 下記リンクにある外側をダブルクォーテーション、内側をシングルクォーテーションにする方法
``` powershell
 -d "{'query': 'hogehoge'}"
```

## 未検証の対策  
- Invoke-RestMethod (powershell版curl)でPOSTする

## 参考
- [PowerShell でcurlを使いたい。JSONをエスケープせずに。 - Qiita](https://qiita.com/aoriso/items/a735f95e771f00825b3e)
- [Invoke-RestMethod(PowerShell版のcUrl)でJsonをPOSTする - 明日の16時](https://itsumoonazicode.hatenablog.com/entry/20180827/1535298452)

</div></details>

<details><summary>windowsのエクスプローラからwslのディレクトリにアクセスする</summary><div>

\\wsl$\Ubuntu

</div></details>

<details><summary>wslとwindowsでgitの資格情報を共有する</summary><div>

`git config --global credential.helper "/mnt/c/Program\ Files/Git/mingw64/libexec/git-core/git-credential-manager.exe"`

※この設定をしてもwindows,wsl両方でgitのssh接続ができるようにはならなかった。  
  何が悪かったのかは不明

</div></details>