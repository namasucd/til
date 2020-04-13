フォーマット
# 結論(タイトル)
## 現象(エラーなど)
## 理由
## 原因
## 対策
## その他
- linkなど

# GitHubの認証をSSHに変えたら、git remote set-urlでリモートリポジトリのURLを修正すること
## HTTPS接続からSSH接続に変更したのに、push時にUsernameとPasswordを求められた
- リモートリポジトリのurlがhttps://github.com/githubユーザ名/githubリポジトリ名.git
  - git remote -v で確認する
## URLを変更する
- '~~https://github.com/~~' → 'git@github.com'
- git remote set-url repositoryname git@github.com:ユーザ名/リポジトリ名.git
## 参考
- [GitHubでssh接続する手順~公開鍵・秘密鍵の生成から~ - Qiita](https://qiita.com/shizuma/items/2b2f873a0034839e47ce)
- [githubを二段階認証に変更後に起こるエラーの対処方法 - Qiita](https://qiita.com/sayama0402/items/670b6b650ebdd8680a0b)

# プログラム開発の基本的な考え方
## フォルダ=プログラムの機能の単位
- NPMなど、パッケージと呼ばれるものは、ソースファイルをまとめたフォルダを単位として機能を提供する。
- package.jsonがその証左
- javaならば plugin.xmlでまとめたプロジェクトか。
## フォルダ=管理の単位
- ルートディレクトリに`git init`することで、フォルダの変更履歴をまとめて管理できる
- 同様に`code .`でまとまった編集を行う

# WSLでファイル操作するためにwsl.confでoption=metadataを指定する
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

# 直前のコミットを修正するgit commit --amend
そのまんま。
## 使いどころ
- 一部のファイルをaddし忘れたとき
- コミットメッセージを変更したいとき
## ※リモートにpushした後に--amendしてはいけない!!
リモートとの整合性が失われてしまうため
## 参考
- [git commit --amend で，ちょっとしたミスをしれっと直す - すこしふしぎ．](http://ism1000ch.hatenablog.com/entry/2014/03/26/190939)

# Billboard Deviceとは、接続先のデバイスを正しく表示できなかった際の名前
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