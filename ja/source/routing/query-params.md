<!--
Query parameters are optional key-value pairs that appear to the right of
the `?` in a URL. For example, the following URL has two query params,
`sort` and `page`, with respective values `ASC` and `2`:
-->

クエリーパラメーターとは、URL中の`?`の後にあるキーと値のペアのことです。
例えば、以下のURLにはクエリーパラメーターが2つあり、`sort`に`ASC`、`page`に`2`の値が指定されています。

```text
http://example.com/articles?sort=ASC&page=2
```

<!--
Query params allow for additional application state to be serialized
into the URL that can't otherwise fit into the _path_ of the URL (i.e.
everything to the left of the `?`). Common use cases for query params include
representing the current page number in a paginated collection, filter criteria, or sorting criteria.
-->

クエリーパラメーターを使うことで、URLのパス(`?`の左側のすべて)に含むことができないアプリケーション状態をURLにシリアライズして表すことができます。
クエリーパラメーターの一般的例は、ページネーションや、検索、ソートなどがあげられえます。

<!--
### Specifying Query Parameters
-->

### クエリーパラメーターの指定

<!--
Query params are declared on route-driven controllers. For example, to
configure query params that are active within the `articles` route,
they must be declared on `controller:articles`.
-->

クエリーパラメーターはコントローラ上で定義します。
`articles`ルート内でクエリーパラメータを有効するには、`controller:articles`で宣言します。

<!--
To add a `category`
query parameter that will filter out all the articles that haven't
been categorized as popular we'd specify `'category'`
as one of `controller:articles`'s `queryParams`:
-->

カテゴリーを指定して記事を絞るような場合、`controller:articles`の`queryParams`に`category`を指定します。

```app/controllers/articles.js
import Controller from '@ember/controller';

export default Controller.extend({
  queryParams: ['category'],
  category: null
});
```

<!--
This sets up a binding between the `category` query param in the URL,
and the `category` property on `controller:articles`. In other words,
once the `articles` route has been entered, any changes to the
`category` query param in the URL will update the `category` property
on `controller:articles`, and vice versa.
Note that you can't bind `queryParams` to computed properties, they
have to be values.
-->

これは、URLの`category`クエリーパラメーターと`controller:articles`の`category`プロパティをバインドします。
言い換えれば、`articles`ルートで、URLの`category`クエリーパラメーターが変更されると、`controller:articles`の`category`プロパティも更新されます。その逆もあります。
`queryParams`は算出プロパティにはバインドできません。プロパティにバインドしなければなりません。

<!--
Now we need to define a computed property of our category-filtered
array that the `articles` template will render:
-->

そして、`articles`テンプレートで表示するカテゴリーで絞られた記事の配列は、算出プロパティとして定義する必要があります。

```app/controllers/articles.js
import Controller from '@ember/controller';
import { computed } from '@ember/object';

export default Controller.extend({
  queryParams: ['category'],
  category: null,

  filteredArticles: computed('category', 'model', function() {
    let category = this.get('category');
    let articles = this.get('model');

    if (category) {
      return articles.filterBy('category', category);
    } else {
      return articles;
    }
  })
});
```

<!--
With this code, we have established the following behaviors:
-->

このコードでは、次のような動作するようにしています。

<!--
1. If the user navigates to `/articles`, `category` will be `null`, so
   the articles won't be filtered.
2. If the user navigates to `/articles?category=recent`,
   `category` will be set to `"recent"`, so articles will be filtered.
3. Once inside the `articles` route, any changes to the `category`
   property on `controller:articles` will cause the URL to update the
   query param. By default, a query param property change won't cause a
   full router transition (i.e. it won't call `model` hooks and
   `setupController`, etc.); it will only update the URL.
-->

1. ユーザーが`/articles`に移動すると、`category`は`null`になります。したがって、記事はフィルタリングされません。
2. ユーザーが`/articles?category=recent`に移動すると、`category`は`"recent"`に設定され、記事はフィルタリングされます。
3. `articles`ルート内で、`controller:articles`の`category`プロパティを変更すると、URL上のクエリパラメーターも更新されます。デフォルトでは、クエリパラメーターのプロパティを変更してもルータの完全なトランジションは発生しません。(例えば、`model`フックや`setupController`などは呼び出されません)URLが更新されるです。

<!--
### link-to Helper
-->

### link-toヘルパー

<!--
The `link-to` helper supports specifying query params using the
`query-params` subexpression helper.
-->

`link-to`ヘルパーで、`query-params`副次式(subexpression)ヘルパーを使用してクエリーパラメーターを指定できます。

```handlebars
// Explicitly set target query params
{{#link-to "posts" (query-params direction="asc")}}Sort{{/link-to}}

// Binding is also supported
{{#link-to "posts" (query-params direction=otherDirection)}}Sort{{/link-to}}
```

--- 誤訳警報:ここから ---

In the above examples, `direction` is presumably a query param property
on the `posts` controller, but it could also refer to a `direction` property
on any of the controllers associated with the `posts` route hierarchy,
matching the leaf-most controller with the supplied property name.

*訳注: 上記の原文をよく理解できなかったため、原文を載せつつ、翻訳も載せていますが、翻訳に間違いがある可能性が高いです。誰か教えてください。(>_<)*

上の例では、`direction`は`posts`コントローラのクエリーパラメータープロパティだと推測できます。
しかし、`posts`ルート階層配下に配置された直近のコントローラのプロパティ名とマッチして、`direction`プロパティを参照することもできます。

--- 誤訳警報:ここまで ---

<!--
The `link-to` helper takes into account query parameters when determining
its "active" state, and will set the class appropriately. The active state
is determined by calculating whether the query params end up the same after
clicking a link. You don't have to supply all of the current,
active query params for this to be true.
-->

`link-to`ヘルパーは、アクティブ状態を判断するときにクエリーパラメーターを考慮に入れ、クラスを適切に設定します
アクティブ状態は、リンクをクリックした後で、クエリパラメータが同じになるかどうかを計算して決定されます。
このためには、現在のアクティブなクエリーパラメーターをすべて指定する必要はありません。

### transitionTo

<!--
`Route#transitionTo` and `Controller#transitionToRoute`
accept a final argument, which is an object with the key `queryParams`.
-->

`Route#transitionTo`および`Controller#transitionToRoute`は、`queryParams`キーを持つオブジェクトを最後の引数で受け取ります。

```app/routes/some-route.js
this.transitionTo('post', object, { queryParams: { showDetails: true }});
this.transitionTo('posts', { queryParams: { sort: 'title' }});

// if you want to transition the query parameters without changing the route
this.transitionTo({ queryParams: { direction: 'asc' }});
```

<!--
You can also add query params to URL transitions:
-->

URLによるトランジションでもクエリーパラメーターを指定できます。

```app/routes/some-route.js
this.transitionTo('/posts/1?sort=date&showDetails=true');
```

<!--
### Opting into a full transition
-->

### ルートのフックメソッドを実行するオプション

<!--
When you change query params through a transition (`transitionTo` and `link-to`),
it is not considered a full transition.
This means that the controller properties associated with the query params will be updated,
as will the URL, but no `Route` method hook like `model` or `setupController` will be called.
-->

トランジション(`transitionTo` and `link-to`)を使用してのクエリー
パラメーターの変更は、完全なトランジションと見なされません。
これは、URLとクエリーパラメーターに関連付けられたコントローラのプロパティは更新されるものの、`Route`の`model`や`setupController`といったメソッドフックが呼び出されないことを意味します。

<!--
If you need a query param change to trigger a full transition, and thus the method hooks,
you can use the optional `queryParams` configuration hash on the `Route`.
If you have a `category` query param and you want it to trigger a model refresh,
you can set it as follows:
-->

クエリーパラメーター変更完でフックメソッドなど完全なトランジションをトリガしたい場合、`Route`で`queryParams`オプションを設定します。
`category`というクエリーパラメーターがあり、モデルの更新をトリガしたい場合は、次のように設定します。

```app/routes/articles.js
import Route from '@ember/routing/route';

export default Route.extend({
  queryParams: {
    category: {
      refreshModel: true
    }
  },

  model(params) {
    // This gets called upon entering 'articles' route
    // for the first time, and we opt into refiring it upon
    // query param changes by setting `refreshModel:true` above.

    // params has format of { category: "someValueOrJustNull" },
    // which we can forward to the server.
    return this.get('store').query('article', params);
  }
});
```

```app/controllers/articles.js
import Controller from '@ember/controller';

export default Controller.extend({
  queryParams: ['category'],
  category: null
});
```

<!--
### Update URL with `replaceState` instead
-->

### `replaceState`でURLを更新する

<!--
By default, Ember will use `pushState` to update the URL in the
address bar in response to a controller query param property change.
If you would like to use `replaceState` instead, which prevents an
additional item from being added to your browser's history,
you can specify this as follows:
-->

デフォルトでは、Emberは`pushState`を使用して、コントローラのクエリーパラメータープロパティの変更に応じてアドレスバーのURLを更新します。
クエリーパラメーターの変更がブラウザの履歴に追加されないにするには、`replaceState`を使います。
次のように指定します。

```app/routes/articles.js
import Route from '@ember/routing/route';

export default Route.extend({
  queryParams: {
    category: {
      replace: true
    }
  }
});
```

<!--
This behaviour is similar to `link-to`,
which also lets you opt into a `replaceState` transition via `replace=true`.
-->

この動作は`link-to`と似ています
`replace=true`を指定すると、`replaceState`によるトランジションになります。

<!--
### Map a controller's property to a different query param key
-->

### コントローラーのプロパティーを別のクエリーパラメーターのキーに割り当てる

<!--
By default, specifying `foo` as a controller query param property will
bind to a query param whose key is `foo`, e.g. `?foo=123`.
You can also map a controller property to a different query param key using the following configuration syntax:
-->

デフォルトでは、コントローラのクエリーqueryParamsプロパティに`foo`指定すると、キーが`foo`であるクエリーパラメーターに(`?foo=123`が)バインドされます。
次の設定構文を使用して、コントローラプロパティを別のクエリパラメータキーにマッピングすることもできます。

```app/controllers/articles.js
import Controller from '@ember/controller';

export default Controller.extend({
  queryParams: {
    category: 'articles_category'
  },

  category: null
});
```

<!--
This will cause changes to the `controller:articles`'s `category`
property to update the `articles_category` query param, and vice versa.
-->

これにより、`controller:articles`の`category`の変更は`articles_category`クエリーパラメーターを更新し、その逆も同様です。

<!--
Query params that require additional customization can
be provided along with strings in the `queryParams` array.
-->

設定が必要なクエリーパラメーターは、`queryParams`配列と内にハッシュで指定します。

```app/controllers/articles.js
import Controller from '@ember/controller';

export default Controller.extend({
  queryParams: ['page', 'filter', {
    category: 'articles_category'
  }],

  category: null,
  page: 1,
  filter: 'recent'
});
```

<!--
### Default values and deserialization
-->

### デフォルト値とデシリアライズによる型変換

<!--
In the following example,
the controller query param property `page` is considered to have a default value of `1`.
-->

次の例では、コントローラのクエリーパラメータープロパティの`page`のデフォルト値は1になります。

```app/controllers/articles.js
import Controller from '@ember/controller';

export default Controller.extend({
  queryParams: 'page',
  page: 1
});
```

<!--
This affects query param behavior in two ways:
-->

デフォルト値は、クエリーパラメーターの動作に2つ影響を与えます。

<!--
1. Query param values are cast to the same datatype as the default
   value, e.g. a URL change from `/?page=3` to `/?page=2` will set
   `controller:articles`'s `page` property to the number `2`, rather than
   the string `"2"`. The same also applies to boolean default values. If the
   default value is an array, the string will be parsed using `JSON.parse`.
2. When a controller's query param property is currently set to its
   default value, this value won't be serialized into the URL. So in the
   above example, if `page` is `1`, the URL might look like `/articles`,
   but once someone sets the controller's `page` value to `2`, the URL
   will become `/articles?page=2`.
-->

1. クエリーパラメーターの値はデフォルト値と同じデータ型にキャストされます。
  例えば、`/?page=3`から`/?page=２`にURLを変更すると、`controller:articles`の`page`プロパティは文字列の`"2"`ではなく数値の`2`に設定されます。
  ブール値のデフォルト値についても同様です。
  デフォルト値が配列の場合、`JSON.parse`を使用して文字列をパーズします。
2. コントローラのqueryParamsプロパティのデフォルト値は、URLにシリアライズされません。
   したがって、上記の例では、`page`が`1`の場合、URLは`/articles`になり、コントローラの`pge`の値を`2`に設定すると
   URLは`/articles?page=2`になります。

<!--
### Sticky Query Param Values
-->

### クエリーパラメーターの値はスティッキー

*訳注: スティッキー(sticky)とはベタベタした、ネバネバしたという意味です。

<!--
By default, query param values in Ember are "sticky",
in that if you make changes to a query param and then leave and re-enter the route,
the new value of that query param will be preserved (rather than reset to its default).
This is a particularly handy default for preserving sort/filter parameters as you navigate back and forth between routes.
-->

デフォルトでは、Emberのクエリパラメータ値は「スティッキー」(定着している)です。
クエリーパラメーター変更後にそのルートから離れてからまた戻ると、クエリーパラメーターは(デフォルト値にリセットされるのではなく)ルートを離れた時の値になっています。
これは、ルート間を行き来する際にソートやフィルタパラメーターを保持しておくのに特に便利です。

<!--
Furthermore, these sticky query param values are remembered/restored according to the model loaded into the route.
So, given a `team` route with dynamic segment `/:team_name` and controller query param "filter",
if you navigate to `/badgers` and filter by `"rookies"`,
then navigate to `/bears` and filter by `"best"`,
and then navigate to `/potatoes` and filter by `"worst"`,
then given the following nav bar links:
-->

さらに、これらのスティッキーなクエリパラメーター値は、ルートにロードされたモデルに従って復元されます。
したがって、`team`ルートに動的セグメント`/:team_name`と、コントローラのクエリーパラメーター"filter"を指定した場合、
`/badgers`に移動して "ルーキー"でフィルタリングし、`/bears`に移動して"best"でフィルタして、
さらに`/potatoes`に移動し、"worst"でフィルタリングすると、以下のナビゲーションバーのリンクは、

```handlebars
{{#link-to "team" "badgers"}}Badgers{{/link-to}}
{{#link-to "team" "bears"}}Bears{{/link-to}}
{{#link-to "team" "potatoes"}}Potatoes{{/link-to}}
```

<!--
the generated links would be:
-->

次のようになります。

```html
<a href="/badgers?filter=rookies">Badgers</a>
<a href="/bears?filter=best">Bears</a>
<a href="/potatoes?filter=worst">Potatoes</a>
```

<!--
This illustrates that once you change a query param,
it is stored and tied to the model loaded into the route.
-->

これは、一度クエリーパラメーターを変更すると、値が保存され、ルートに読み込まれたモデルに結びついていることを示しています。

<!--
If you wish to reset a query param, you have two options:
-->

クエリーパラメーターをリセットする方法は2つあります。

<!--
1. explicitly pass in the default value for that query param into
   `link-to` or `transitionTo`.
2. use the `Route.resetController` hook to set query param values back to
   their defaults before exiting the route or changing the route's model.
-->

1. クエリーパラメーターのデフォルト値を明示的に`link-to`または`transitionTo`に渡す
2. `Route.resetController`フックを使用して、ルートを出る前かルートのモデルを変更する前に、クエリーパラメーターの値をデフォルトに戻す

<!--
In the following example, the controller's `page` query param is reset to 1,
_while still scoped to the pre-transition `ArticlesRoute` model_.
The result of this is that all links pointing back into the exited route will use the newly reset value `1` as the value for the `page` query param.
-->

次の例では、コントローラーのクエリーパラメーターの`page`は1にリセットされますが、
引き続きトランジション前の`ArticlesRoute`モデルにスコープされています。
この結果、離れたルートへのリンクは、新たにリセットされた値である`1`をページクエリーパラメーターの値として使用することになります。

<!--
```app/routes/articles.js
import Route from '@ember/routing/route';

export default Route.extend({
  resetController(controller, isExiting, transition) {
    if (isExiting) {
      // isExiting would be false if only the route's model was changing
      controller.set('page', 1);
    }
  }
});
```
-->
```app/routes/articles.js
import Route from '@ember/routing/route';

export default Route.extend({
  resetController(controller, isExiting, transition) {
    if (isExiting) {
      // モデルの変更のみの場合はisExitingはfalseになります
      controller.set('page', 1);
    }
  }
});
```

<!--
In some cases, you might not want the sticky query param value to be
scoped to the route's model but would rather reuse a query param's value
even as a route's model changes. This can be accomplished by setting the
`scope` option to `"controller"` within the controller's `queryParams`
config hash:
-->

場合によっては、クエリーパラメーターのスティッキーな値をルートのモデルにスコープするのではなく、ルートのモデルの変更時に再利用したいことがあるでしょう。
これはコントローラの`queryParams`で`scope`オプションを`"controller"`に設定することで実現できます。

```app/controllers/articles.js
import Controller from '@ember/controller';

export default Controller.extend({
  queryParams: [{
    showMagnifyingGlass: {
      scope: 'controller'
    }
  }]
});
```

<!--
The following demonstrates how you can override both the scope and the query param URL key of a single controller query param property:
-->

次の例では、1つのクエリーパラメータープロパティのスコープとURL上のクエリのパラメーターキーの両方をオーバーライドする方法を示しています。

```app/controllers/articles.js
import Controller from '@ember/controller';

export default Controller.extend({
  queryParams: ['page', 'filter',
    {
      showMagnifyingGlass: {
        scope: 'controller',
        as: 'glass'
      }
    }
  ]
});
```
