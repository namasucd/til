# RESTAPIを使ってみる - Github Docs

## 参照
### URL
[REST APIを使ってみる - GitHub Docs](https://docs.github.com/ja/free-pro-team@latest/rest/guides/getting-started-with-the-rest-api)

## 復習用チェックリスト

<details><summary>APIを使って任意のユーザーの情報をGETできる</summary><div>

`curl -i https://api.github.com/users/namasucd`
</div></details>

<details><summary>認証用の個人アクセストークンが取得できる</summary><div>

- 個人設定→DeveloperなんちゃらSettings→Personal Access Tokens
- 作成後、入手したトークン(文字列)を環境変数に登録
  - コードに埋め込んだり、CLIの履歴に残ったりするのはセキュリティ上好ましくないため
</div></details>


<details><summary>認証が必要なユーザーのリポジトリ情報(issue,ラベルで絞り込み)が取得できる</summary><div>

`curl -H "Authorization: token $env:TOKEN_GITHUB" "https://api.github.com/repos/username/repositoryname/issues?labels='daily'"`
- -HでヘッダのAutorizationにトークンを渡す
- reposにもissuesも複数形
- クエリを表す?を使う場合はcURLをダブルクォーテーションでくくる
</div></details>

## 追加学習事項
- 任意のissueについたコメントを取得する
- 取得した情報が文字化けする問題の解決方法を得る
