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