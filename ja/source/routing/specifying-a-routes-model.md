<!--
Often, you'll want a template to display data from a model. Loading the
appropriate model is one job of a route.
-->

テンプレートでモデルのデータを表示したいことが多々あります。
適切なモデルを読み込むことは、ルートの仕事の1つです。

例えば、次のルータを使用します。

<!--
For example, take this router:
-->

```app/router.js
Router.map(function() {
  this.route('favorite-posts');
});
```

<!--
To load a model for the `favorite-posts` route, you would use the [`model()`](https://www.emberjs.com/api/ember/2.16/classes/Route/methods/model?anchor=model)
hook in the `favorite-posts` route handler:
-->

`favorite-posts`ルートのモデルを読み込むには、`favorite-posts`ルートハンドラで[`model()`](https://www.emberjs.com/api/ember/2.16/classes/Route/methods/model?anchor=model)フックを使います：

```app/routes/favorite-posts.js
import Route from '@ember/routing/route';

export default Route.extend({
  model() {
    return this.get('store').query('post', { favorite: true });
  }
});
```

<!--
Typically, the `model` hook should return an [Ember Data](../../models/) record,
but it can also return any [promise](https://www.promisejs.org/) object (Ember Data records are promises),
or a plain JavaScript object or array.
Ember will wait until the data finishes loading (until the promise is resolved) before rendering the template.
-->

通常、`model`フックは[Ember Data](../../models/)レコードを返すべきですが、[Promise](https://www.promisejs.org/)オブジェクト(Ember DataレコードはPromiseです)、またはプレーンJavaScriptオブジェクトか配列を返すこともできます。
Emberは、テンプレートを描画する前にデータのロードの完了を(Promiseが解決されるのを)待ちます。

<!--
The route will then set the return value from the `model` hook as the `model` property of the controller.
You will then be able to access the controller's `model` property in your template:
-->

ルートは、`model`フックからの戻り値をコントローラの`model`プロパティとして設定します。
その後、テンプレートでコントローラの`model`プロパティにアクセスできるようになります。

```app/templates/favorite-posts.hbs
<h1>Favorite Posts</h1>
{{#each model as |post|}}
  <p>{{post.body}}</p>
{{/each}}
```

<!--
## Dynamic Models
-->

## 動的モデル


<!--
Some routes always display the same model. For example, the `/photos`
route will always display the same list of photos available in the
application. If your user leaves this route and comes back later, the
model does not change.
-->

ルートによっては、常に同じモデルが表示されるものがあります。
例えば、`/photos`ルートにいつも同じ写真一覧を表示してる場合、
ユーザーがこのルートを離れて後で戻ってきても、モデルが変更されることはありません。

<!--
However, you will often have a route whose model will change depending
on user interaction. For example, imagine a photo viewer app. The
`/photos` route will render the `photos` template with the list of
photos as the model, which never changes. But when the user clicks on a
particular photo, we want to display that model with the `photo`
template. If the user goes back and clicks on a different photo, we want
to display the `photo` template again, this time with a different model.
-->

ですが、ユーザーの操作に応じてモデルが変更されるルートがあることがよくあります。
例えば、写真表示アプリを想像してみてください。
`/photos`ルートは`photos`テンプレートをモデルとして描画します。モデルは変更されません。
しかし、ユーザーが特定の写真をクリックすると、`photo`テンプレートを使用してそのモデルを表示したり、
ユーザーが別の写真に戻ってクリックすると、今度は別のモデルで写真テンプレートを再度表示したいことがあります。

<!--
In cases like this, it's important that we include some information in
the URL about not only which template to display, but also which model.
-->

その場合、表示するテンプレートだけでなく、モデルについての情報もURLに含めることが重要です。

<!--
In Ember, this is accomplished by defining routes with [dynamic
segments](../defining-your-routes/#toc_dynamic-segments).
-->

Emberでは、[動的セグメント](../defining-your-routes/#toc_dynamic-segments)を使ったルートの定義で
実現できます。

<!--
Once you have defined a route with a dynamic segment,
Ember will extract the value of the dynamic segment from the URL for
you and pass them as a hash to the `model` hook as the first argument:
-->

動的セグメントを持つルートを定義すると、EmberはURLから動的セグメントの値を抽出し、ハッシュとして`model`フックに第1引数として渡します。

```app/router.js
Router.map(function() {
  this.route('photo', { path: '/photos/:photo_id' });
});
```

```app/routes/photo.js
import Route from '@ember/routing/route';

export default Route.extend({
  model(params) {
    return this.get('store').findRecord('photo', params.photo_id);
  }
});
```

<!--
In the `model` hook for routes with dynamic segments, it's your job to
turn the ID (something like `47` or `post-slug`) into a model that can
be rendered by the route's template. In the above example, we use the
photo's ID (`params.photo_id`) as an argument to Ember Data's `findRecord`
method.
-->

動的セグメントを持つルートの`model`フックでは、ID（`47`または`article-title`のようなもの)をルートテンプレートで使うモデルに変換するなどの実装が必要です。
上記の例では、Ember Dataの`findRecord`メソッドの引数として、写真のID(`params.photo_id`)を使用しています。

<!--
Note: A route with a dynamic segment will always have its `model` hook called when it is entered via the URL.
If the route is entered through a transition (e.g. when using the [link-to](../../templates/links) Handlebars helper),
and a model context is provided (second argument to `link-to`), then the hook is not executed.
If an identifier (such as an id or slug) is provided instead then the model hook will be executed.
-->

注意: 動的セグメントを持つルートは、URLを介して入ったときに呼び出される`model`フックを常に持ちます。 
例えば、([link-to](../../templates/links)ヘルパーを使用するときなど)ルートに遷移させる際、(`link-to`の第2引数に)モデルを渡すと、`model`フックは実行されません。
識別子(IDやスラッグなど)が渡されている場合は、 `model`フックは実行されます。

<!--
For example, transitioning to the `photo` route this way won't cause the `model` hook to be executed (because `link-to`
was passed a model):
-->

例えば、以下の方法で`photo`ルートに遷移すると`model`フックは実行されません。(`link-to`にモデルを渡しているため)

```app/templates/photos.hbs
<h1>Photos</h1>
{{#each model as |photo|}}
  <p>
    {{#link-to "photo" photo}}
      <img src="{{photo.thumbnailUrl}}" alt="{{photo.title}}" />
    {{/link-to}}
  </p>
{{/each}}
```

<!--
while transitioning this way will cause the `model` hook to be executed (because `link-to` was passed `photo.id`, an
identifier, instead):
-->

以下ように遷移すると`model`フックが実行されます。(link-toには識別子である`photo.id`が渡されるため)

```app/templates/photos.hbs
<h1>Photos</h1>
{{#each model as |photo|}}
  <p>
    {{#link-to "photo" photo.id}}
      <img src="{{photo.thumbnailUrl}}" alt="{{photo.title}}" />
    {{/link-to}}
  </p>
{{/each}}
```

<!--
Routes without dynamic segments will always execute the model hook.
-->

動的セグメントのないルートは常に`model`フックを実行します。

<!--
## Multiple Models
-->

## 複数のモデル

<!--
Multiple models can be returned through an
[RSVP.hash](https://www.emberjs.com/api/ember/2.16/classes/rsvp/methods/hash?anchor=hash).
The `RSVP.hash` method takes an object with promises or values as properties as an argument, and returns a single promise.
When all of the promises in the object resolve, the returned promise will resolve with an object of all of the promise values. For example:
-->

複数のモデルは、[RSVP.hash](https://www.emberjs.com/api/ember/2.16/classes/rsvp/methods/hash?anchor=hash)を使って返すことができます。
`RSVP.hash`は、引数に`Promise`か値を持つオブジェクトをとり、`Promise`を返します。
返されたPromiseは、オブジェクト内の`Promise`が全て解決されると、promiseで解決された値を入れたオブジェクトで解決されます。
例:
```app/routes/songs.js
import Route from '@ember/routing/route';
import RSVP from 'rsvp';

export default Route.extend({
  model() {
    return RSVP.hash({
      songs: this.get('store').findAll('song'),
      albums: this.get('store').findAll('album')
    });
  }
});
```

<!--
In the `songs` template, we can specify both models and use the `{{#each}}` helper to display
each record in the song model and album model:
-->

`songs`テンプレートでは、両方のモデルを指定し、`{{#each}}`ヘルパーを使用して、songモデルとalbumモデルの各レコードを表示できます。

```app/templates/songs.hbs
<h1>Playlist</h1>

<ul>
  {{#each model.songs as |song|}}
    <li>{{song.name}} by {{song.artist}}</li>
  {{/each}}
</ul>

<h1>Albums</h1>

<ul>
  {{#each model.albums as |album|}}
    <li>{{album.title}} by {{album.artist}}</li>
  {{/each}}
</ul>
```

<!--
If you use [Ember Data](../../models/) and you are building an `RSVP.hash` with the model's relationship, consider instead properly setting up your [relationships](../../models/relationships) and letting Ember Data take care of loading them.
-->

[Ember Data](../../models/)を使用していて、モデルの関係(relationships)で`RSVP.hash`を作成している場合は、`RSVP.hash`を使うのではなく、代わりに[relationships](../../models/relationships)を適切に設定し、Ember Dataがそれらの読み込みを処理できるようにしてください。

<!--
## Reusing Route Context
-->

## ルートコンテキストの再利用

<!--
Sometimes you need to fetch a model, but your route doesn't have the parameters, because it's
a child route and the route directly above or a few levels above has the parameters that your route
needs.
-->

モデルを取得する必要があっても、そのルートではパラメーターを取得できない場合あります。
子ルートではパラメーターにアクセスできず、親ルートでできることがあります。

<!--
In this scenario, you can use the `paramsFor` method to get the parameters of a parent route.
-->

その場合、`paramsFor`メソッドを使用して親ルートのパラメーターを取得できます。

```app/routes/album/index.js
import Route from '@ember/routing/route';

export default Route.extend({
  model() {
    let { album_id } = this.paramsFor('album');

    return this.store.query('song', { album: album_id });
  }
});
```

<!--
This is guaranteed to work because the parent route is loaded. But if you tried to
do `paramsFor` on a sibling route, you wouldn't have the results you expected.
-->

子ルートでは、親ルートはロードされているため、これは動作することが保証されています。
しかし、兄弟ルートで`paramsFor`をしようとすると、期待した結果が得られないでしょう。

<!--
This is a great way to use the parent context to load something that you want.
Using `paramsFor` will also give you the query params defined on that route's controller.
This method could also be used to look up the current route's parameters from an action
or another method on the route, and in that case we have a shortcut: `this.paramsFor(this.routeName)`.
-->

`paramsFor`で親ルートから必要なものを読み込むのは最適なやり方です。
`paramsFor`は、そのルートのコントローラで定義されたクエリーパラメーターも得られます。
このメソッドは、ルート上のアクションか別のメソッドから現在のルートのパラメーターを取得するためにも使用できます
その場合は、`this.paramsFor(this.routeName)`というショートカットがあります。

<!--
In our case, the parent route had already loaded its songs, so we would be writing unnecessary fetching logic.
Let's rewrite the same route, but use `modelFor`, which works the same way, but returns the model
from the parent route.
-->

上記の例の場合、親ルートには既に曲がロードされていますが、子ルートに不要なデータ取得のコードを書くこともできます。
ルートを書き直してみましょう。
ですが今回は、`modelFor`を使い、親ルートからモデルを返して、同じように動作するようにします。


```app/routes/album/index.js
import Route from '@ember/routing/route';

export default Route.extend({
  model() {
    let { songs } = this.modelFor('album');

    return songs;
  }
});
```

<!--
In the case above, the parent route looked something like this:
-->

上記の場合、親ルートは次のようになります。

```app/routes/album.js
import Route from '@ember/routing/route';
import RSVP from 'rsvp';

export default Route.extend({
  model({ album_id }) {
    return RSVP.hash({
      album: this.store.findRecord('album', album_id),
      songs: this.store.query('songs', { album: album_id })
    });
  }
});
```

<!--
And calling `modelFor` returned the result of the `model` hook.
-->

`modelFor`を呼び出すと、`model`フックの結果が返されます。
