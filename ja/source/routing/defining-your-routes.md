<!--
When your application starts, the router matches the current URL to the _routes_
that you've defined. The routes, in turn, are responsible for displaying
templates, loading data, and setting up application state.
-->

アプリケーションが起動すると、ルータは現在のURLを定義したルートに一致させます。
ルートは、テンプレートの表示、データのロード、アプリケーション状態の設定を担当します。

<!--
To define a route, run
-->

ルートを定義するには、まず以下を実行してください。

```shell
ember generate route route-name
```

<!--
This creates a route file at `app/routes/route-name.js`, a template for the route at `app/templates/route-name.hbs`,
and a unit test file at `tests/unit/routes/route-name-test.js`.
It also adds the route to the router.
-->

これにより、ルートファイル`app/routes/route-name.js`と、ルートのテンプレート`app/templates/route-name.hbs`と、ユニットテストファイル(`tests/unit/routes/route-name-test.js`)が作成されます。
また、ルータにルートを追加します。

<!--
## Basic Routes
-->

## ルートの基本

<!--
The [`map()`](https://www.emberjs.com/api/ember/2.16/classes/Router/methods/map?anchor=map) method
of your Ember application's router can be invoked to define URL mappings. When
calling `map()`, you should pass a function that will be invoked with the value
`this` set to an object which you can use to create routes.
-->

Emberアプリケーションのルータの
The [`map()`](https://www.emberjs.com/api/ember/2.16/classes/Router/methods/map?anchor=map)メソッドを呼び出して、URLマッピングを定義することができます。
`map()`を呼び出すときには、ルートを作成するのに使う`this`を使用した関数を渡します。

```app/router.js
Router.map(function() {
  this.route('about', { path: '/about' });
  this.route('favorites', { path: '/favs' });
});
```

<!--
Now, when the user visits `/about`, Ember will render the `about`
template. Visiting `/favs` will render the `favorites` template.
-->

ユーザーが`/about`を訪れたとき、Emberは`about`テンプレートを描画します。
訪問中の `/favs` は`favorites`テンプレートを描画します。

<!--
You can leave off the path if it is the same as the route
name. In this case, the following is equivalent to the above example:
-->

パスがルート名と同じ場合は、パスは省略できます。
この場合、以下は上記の例と同じです。

```app/router.js
Router.map(function() {
  this.route('about');
  this.route('favorites', { path: '/favs' });
});
```

<!--
Inside your templates, you can use [`{{link-to}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/link-to?anchor=link-to) to navigate between
routes, using the name that you provided to the `route` method.
-->

テンプレート内では、[`{{link-to}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/link-to?anchor=link-to)を使用して、`route`メソッドに指定した名前を使用して各ルートにリンクできます。

```handlebars
{{#link-to "index"}}<img class="logo">{{/link-to}}

<nav>
  {{#link-to "about"}}About{{/link-to}}
  {{#link-to "favorites"}}Favorites{{/link-to}}
</nav>
```

<!--
The `{{link-to}}` helper will also add an `active` class to the link that
points to the currently active route.
-->

`{{link-to}}`ヘルパーは、現在アクティブなルートにリンクしている場合、クラス属性に`active`を追加します。

<!--
Multi-word route names are conventionally dasherized, such as:
-->

複数の単語からなるのルート名は、規約上以下のようにダッシュを使って繋げます。

```app/router.js
Router.map(function() {
  this.route('blog-post', { path: '/blog-post' });
});
```

<!--
The route defined above will by default use the `blog-post.js` route handler,
the `blog-post.hbs` template, and be referred to as `blog-post` in any
`{{link-to}}` helpers.
-->

上記で定義されたルートは、デフォルトで`blog-post.js`ルートハンドラ、`blog-post.hbs`テンプレートを使用され、`{{link-to}}`ヘルパーには`blog-post`を渡します。

<!--
Multi-word route names that break this convention, such as:
-->

以下はアンダースコアで単語を連結して規則を破った例です。

```app/router.js
Router.map(function() {
  this.route('blog_post', { path: '/blog-post' });
});
```

<!--
will still by default use the `blog-post.js` route handler and the
`blog-post.hbs` template, but will be referred to as `blog_post` in any
`{{link-to}}` helpers.
-->

これはデフォルトで `blog-post.js`ルートハンドラーと`blog-post.hbs`テンプレートを使用しますが、`{{link-to}}`ヘルパーでは `blog_post`を渡さなければなりません。

<!--
## Nested Routes
-->

## ネストしたルート

*訳注: ネスト(nest)は入れ子という意味です。*

<!--
Often you'll want to have a template that displays inside another template.
For example, in a blogging application, instead of going from a list of blog
posts to creating a new post, you might want to have the post creation page
display next to the list.
-->

他のテンプレートの中にテンプレートを表示したい場合がよくあります。
例えば、ブログアプリケーションで、記事一覧から新規投稿のページに進むのではなく、
新規投稿のページを記事一覧の横に表示したいといったケースです。

<!--
In these cases, you can use nested routes to display one template inside
of another.
-->

このような場合、ネストしたルートを使って、別のテンプレートの中にテンプレートを表示することができます。

<!--
You can define nested routes by passing a callback to `this.route`:
-->

コールバックを`this.route`に渡すことでネストしたルートを定義することができます。

```app/router.js
Router.map(function() {
  this.route('posts', function() {
    this.route('new');
  });
});
```

<!--
Assuming you have already generated the `posts` route, to generate the above nested route you would run:
-->

すでに`posts`ルートを生成していると仮定して、上記のネストしたルートを生成するには、次のように実行します。

```shell
ember generate route posts/new
```

<!--
And then add the `{{outlet}}` helper to your template where you want the nested
template to display:
-->

次に、テンプレート内でネストしたテンプレートを表示する所に`{{outlet}}`ヘルパーを追加します。

```templates/posts.hbs
<h1>Posts</h1>
<!-- 記事と他のコンテンツを表示 -->
{{outlet}}
```

<!--
This router creates a route for `/posts` and for `/posts/new`. When a user
visits `/posts`, they'll simply see the `posts.hbs` template. (Below, [index
routes](#toc_index-routes) explains an important addition to this.) When the
user visits `posts/new`, they'll see the `posts/new.hbs` template rendered into
the `{{outlet}}` of the `posts` template.
-->

ルータは、`/posts`と`/posts/new`ルートを作成します。
ユーザーが`/posts`にアクセスすると、`posts.hbs`テンプレートが表示されます。
（下記の[インデックスルート](#toc_インデックスルート)では重要なことが説明されています）。
ユーザーが`posts/new`にアクセスすると、`posts/new.hbs`テンプレートが`posts`テンプレートの`{{outlet}}`に表示されます。

<!--
A nested route name includes the names of its ancestors.
If you want to transition to a route (either
via `transitionTo` or `{{#link-to}}`), make sure to use the full route
name (`posts.new`, not `new`).
-->

ネストしたルート名には、その祖先の名前が含まれます。
ルートに遷移させる場合は(`transitionTo`または`{{#link-to}}`のいずれかで)、完全なルート名(この場合、`new`ではなく`posts.new`)を指定する必要があります。

<!--
## The application route
-->

## アプリケーションルート

<!--
The `application` route is entered when your app first boots up. Like other
routes, it will load a template with the same name (`application` in
this case) by default.
You should put your header, footer, and any other decorative content
here. All other routes will render
their templates into the `application.hbs` template's `{{outlet}}`.
-->

アプリケーションルートは、アプリが最初に起動したときに経由されます。
他のルートと同様に、デフォルトで同じ名前(この場合は`application`)のテンプレートをロードします。
`application`テンプレートには、ヘッダー、フッター、その他の装飾的な内容を入れてください。
他のすべてのルートのテンプレートは`application.hbs`テンプレートの`{{outlet}}`に描画されます。

<!--
This route is part of every application, so you don't need to
specify it in your `app/router.js`.
-->

このルートはアプリケーションの一部なので、`app/router.js`で指定する必要はありません。

<!--
## Index Routes
-->

## インデックスルート

<!--
At every level of nesting (including the top level), Ember
automatically provides a route for the `/` path named `index`.
To see when a new level of nesting occurs, check the router,
whenever you see a `function`, that's a new level.
-->

ネストしたすべての階層(トップ階層を含む)で、Emberは自動的に`/`をインデックスルートという名前で持ちます。
新しい階層のネスティングが発生しているかを確認するには、ルーターをチェックしてます。
`function`があるところは、新しい階層が作られています。

<!--
For example, if you write a simple router like this:
-->

たとえば、次のようなシンプルなルータを定義したとします。

```app/router.js
Router.map(function() {
  this.route('favorites');
});
```

<!--
It is the equivalent of:
-->

これは次のものと同じです。

```app/router.js
Router.map(function() {
  this.route('index', { path: '/' });
  this.route('favorites');
});
```

<!--
The `index` template will be rendered into the `{{outlet}}` in the
`application` template. If the user navigates to `/favorites`,
Ember will replace the `index` template with the `favorites`
template.
-->

`index`テンプレートは、`application`テンプレートの`{{outlet}}`に描画されます。
ユーザーが``/favorites`にアクセスすると、Emberは`index`テンプレートを`favorites`テンプレートに置き換えます。

<!--
A nested router like this:
-->

次のようなネストしたルータは、

```app/router.js
Router.map(function() {
  this.route('posts', function() {
    this.route('favorites');
  });
});
```

<!--
Is the equivalent of:
-->

以下と同じです。

```app/router.js
Router.map(function() {
  this.route('index', { path: '/' });
  this.route('posts', function() {
    this.route('index', { path: '/' });
    this.route('favorites');
  });
});
```

<!--
If the user navigates to `/posts`, the current route will be
`posts.index`, and the `posts/index` template
will be rendered into the `{{outlet}}` in the `posts` template.
-->

ユーザーが`/posts`に移動すると、現在のルートは`posts.index`になり、`posts/index`テンプレートが`posts`テンプレートの`{{outlet}}`に描画されます。

<!--
If the user then navigates to `/posts/favorites`, Ember will
replace the `{{outlet}}` in the `posts` template with the
`posts/favorites` template.
-->

その後、ユーザーが`/posts/favorites`に移動すると、Emberは`posts`テンプレートの`{{outlet}}`を`posts/favorites`テンプレートに置き換えます。

<!--
## Dynamic Segments
-->

## 動的セグメント

<!--
One of the responsibilities of a route is to load a model.
-->

ルートの責任の1つは、モデルをロードすることです。

<!--
For example, if we have the route `this.route('posts');`, our
route might load all of the blog posts for the app.
-->

たとえば、ルートが`this.route('posts');`の場合、ルートがアプリのすべてのブログ記事を読み込む可能性があります。

<!--
Because `/posts` represents a fixed model, we don't need any
additional information to know what to retrieve.  However, if we want a route
to represent a single post, we would not want to have to hardcode every
possible post into the router.
-->

`/posts`は決まったモデルを表しているので、何を取得するかを知るための追加情報は必要ありません。
しかし、ルートで1件の記事を表示する場合、全ての記事のURLをルータにハードコードしたくはありません。

<!--
Enter _dynamic segments_.
-->

そこで動的セグメントの出番です。

<!--
A dynamic segment is a portion of a URL that starts with a `:` and is followed by an identifier.
-->

動的セグメントはURLの一部で、`:`で始め、その後に識別子を付けます。

```app/router.js
Router.map(function() {
  this.route('posts');
  this.route('post', { path: '/post/:post_id' });
});
```

<!--
If the user navigates to `/post/5`, the route will then have the `post_id` of
`5` to use to load the correct post.
Ember follows the convention of `:model-name_id` for two reasons.
The first reason is that Routes know how to fetch the right model by default, if you follow the convention.
The second is that `params` is an object, and can only have one value associated with a key.
To put it in code, the following will *not* work properly:
-->

ユーザーが`/post/5`に移動すると、ルートの`:post_id`が5になり、該当記事を読み込むことができます。
Emberは、2つの理由から、`:model-name_id`の規約に従います。
第1の理由は、あなたが規約に従えば、ルートがデフォルトで適切なモデルをフェッチする方法を知っていることです。
もう一つは、`params`はオブジェクトであり、キーと値は1対1です。
キーが重複していると正しく動作しません。以下はその例です。

```app/router.js
Router.map(function() {
  this.route('photo', { path: '/photo/:id' }, function() {
    this.route('comment', { path: '/comment/:id' });
  });
});
```

<!--
But the following will:
-->

以下は適切に機能します。

```app/router.js
Router.map(function() {
  this.route('photo', { path: '/photo/:photo_id' }, function() {
    this.route('comment', { path: '/comment/:comment_id' });
  });
});
```

<!--
In the next section, [Specifying a Route's Model](../specifying-a-routes-model), you will learn more about how to load a model.
-->

次のセクションの[ルートのモデルの指定する](../specifying-a-routes-model)では、モデルをロードする方法の詳しく学びます。

<!--
## Wildcard / globbing routes
-->

## ワイルドカード/globを使ったルート

<!--
You can define wildcard routes that will match multiple URL segments.
This could be used, for example, if you'd like a catch-all route which is useful when the user enters an incorrect URL not managed by your app.
Wildcard routes begin with an asterisk.
-->

複数のURLセグメントに一致するワイルドカードルートを定義できます。
例えば、ユーザーがアプリで管理していない間違ったURLを入力したときに便利なキャッチオールルートなどに使用できます。
ワイルドカードルートはアスタリスクで始まります。


```app/router.js
Router.map(function() {
  this.route('not-found', { path: '/*path' });
});
```

<!--
```app/templates/not-found.hbs
<p>Oops, the page you're looking for wasn't found</p>
```
-->

```app/templates/not-found.hbs
<p>おっと、指定したURLが見つかりませんでした。</p>
```

<!--
In the above example we have successfully used a wildcard route to handle all routes not managed by our application
so that when a user navigates to `/a/non-existent/path` they will be shown a message that says the page they're looking for wasn't found.
-->

上記の例では、アプリケーションが管理していないすべてのルートを処理するためにワイルドカードルートをうまく使っています。
そのため、ユーザーが/`/a/non-existent/path`に移動すると、探しているページが見つからなかったというメッセージが表示されます。

<!--
## Route Handlers
-->

## ルートハンドラ

<!--
To have your route do something beyond render a template with the same name, you'll
need to create a route handler. The following guides will explore the different
features of route handlers. For more information on routes, see the API documentation
for [the router](https://www.emberjs.com/api/ember/release/classes/Router) and for [route
handlers](https://www.emberjs.com/api/ember/release/classes/Route).
-->

ルートに同じ名前のテンプレートを描画する以外に何かをさせるには、ルートハンドラを作成する必要があります。
次のガイドでは、ルートハンドラのさまざまな機能について説明します。
ルートの詳細については、[ルータ](https://www.emberjs.com/api/ember/release/classes/Router)および[ルートハンドラ](https://www.emberjs.com/api/ember/release/classes/Route)のAPIドキュメントを参照してください。
