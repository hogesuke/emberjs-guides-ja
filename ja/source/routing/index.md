<!--
Imagine we are writing a web app for managing a blog. At any given time, we
should be able to answer questions like _What post are they looking at?_ and
_Are they editing it?_ In Ember.js, the answer to these questions is determined
by the URL.
-->

私たちがブログを管理するためのWebアプリを作っているとしましょう。
ユーザーがどの記事を見ているか？ユーザーはその記事を編集しているのか？という質問が出た時に、
いつでも答えられなければなりません。
Ember.jsでは、これらの質問に対する答えはURLによって決まります。

<!--
The URL can be set in a few ways:
-->

URLはいくつかの方法で設定できます。

<!--
* The user loads the app for the first time.
* The user changes the URL manually, such as by clicking the back button or by
editing the address bar.
* The user clicks a link within the app.
* Some other event in the app causes the URL to change.
-->

* ユーザーが最初にアプリを読み込む
* ユーザーが戻るボタンをクリックするか、アドレスバーを編集するなどして、URLを手動で変更する
* ユーザーがアプリ内のリンクをクリックする
* アプリ内の他のイベントによってURLが変更される

<!--
Regardless of how the URL becomes set, the Ember router then maps the current
URL to one or more route handlers. A route handler can do several things:
-->

URLの設定方法に関係なく、Emberルータは現在のURLを1つまたは複数のルートハンドラにマップします。
ルートハンドラは以下のことができます。

<!--
* It can render a template.
* It can load a model that is then available to the template.
* It can redirect to a new route, such as if the user isn't allowed to visit
that part of the app.
* It can handle actions that involve changing a model or transitioning to a new route.
-->

* テンプレートを描画する
* テンプレートで利用するモデルをロードする
* ユーザーが閲覧する権限がない場合などで、新しいルートにリダイレクトする
* モデルを保存したり、新しいルートに移行したりする操作を処理できる
