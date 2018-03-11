<!--
## The `{{link-to}}` Component
-->

## `{{link-to}}`コンポーネント

<!--
You create a link to a route using the
[`{{link-to}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/get?anchor=link-to)
component.
-->

[`{{link-to}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/get?anchor=link-to)コンポーネントを使って、ルートへリンクできます。

```app/router.js
Router.map(function() {
  this.route('photos', function(){
    this.route('edit', { path: '/:photo_id' });
  });
});
```

```app/templates/photos.hbs
<ul>
  {{#each photos as |photo|}}
    <li>{{#link-to "photos.edit" photo}}{{photo.title}}{{/link-to}}</li>
  {{/each}}
</ul>
```

<!--
If the model for the `photos` template is a list of three photos, the
rendered HTML would look something like this:
-->

`photos`テンプレートのモデルが3枚の写真のリストである場合、描画されたHTMLは次のようになります。

```html
<ul>
  <li><a href="/photos/1">Happy Kittens</a></li>
  <li><a href="/photos/2">Puppy Running</a></li>
  <li><a href="/photos/3">Mountain Landscape</a></li>
</ul>
```

<!--
The `{{link-to}}` component takes one or two arguments:
-->

`{{link-to}}`コンポーネントは1つまたは2つの引数をとります。

<!--
* The name of a route. In this example, it would be `index`, `photos`, or
  `photos.edit`.
* At most one model for each [dynamic segment](../../routing/defining-your-routes/#toc_dynamic-segments).
  By default, Ember.js will replace each segment with the value of the corresponding object's `id` property.
  In the example above, the second argument is each `photo` object, and the `id` property is used to fill in
  the dynamic segment with either `1`, `2`, or `3`. If there is no model to pass to the component, you can provide
  an explicit value instead:
-->

* ルートの名前。 上記の例では、`index`、`photos`、`photos.edit`。
* [動的セグメント](../../routing/defining-your-routes/#toc_動的セグメント)毎に1モデル。
  デフォルトでは、Ember.jsは各セグメントを対応するオブジェクトの`id`プロパティの値で置き換えます。
  上記の例では、第二引数の`photo`オブジェクトの`id`プロパティが動的セグメントに`1`、`2`、
  `3`と置き換えるのに使用されます。
  コンポーネントに渡すモデルがない場合は、代わりに明示的な値を指定できます。

```app/templates/photos.hbs
{{#link-to "photos.edit" 1}}
  First Photo Ever
{{/link-to}}
```

<!--
When the rendered link matches the current route, and the same
object instance is passed into the component, then the link is given
`class="active"`. For example, if you were at the URL `/photos/2`,
the first example above would render as:
-->

描画されたリンクが現在のルートと一致し、かつ同じオブジェクトインスタンスがコンポーネントに渡されていると、
そのリンクは`class="active"`となります。
例えば、閲覧中のページのURLが`/photos/2`の場合、上記の最初の例は次のように表示されます。

```html
<ul>
  <li><a href="/photos/1">Happy Kittens</a></li>
  <li><a href="/photos/2" class="active">Puppy Running</a></li>
  <li><a href="/photos/3">Mountain Landscape</a></li>
</ul>
```

<!--
### Example for Multiple Segments
-->

### 複数セグメントの例

<!--
If the route is nested, you can supply a model or an identifier for each dynamic
segment.
-->

ネストしたルートの場合は、各動的セグメントにモデルか識別子を指定できます。

```app/router.js
Router.map(function() {
  this.route('photos', function(){
    this.route('photo', { path: '/:photo_id' }, function(){
      this.route('comments');
      this.route('comment', { path: '/comments/:comment_id' });
    });
  });
});
```

```app/templates/photo/index.hbs
<div class="photo">
  {{body}}
</div>

<p>{{#link-to "photos.photo.comment" primaryComment}}Main Comment{{/link-to}}</p>
```

<!--
If you specify only one model, it will represent the innermost dynamic segment `:comment_id`.
The `:photo_id` segment will use the current photo.
-->

モデルを1つだけ渡すと、最も内側の動的セグメントの`:comment_id`だけを指定したことになります。
`:photo_id`セグメントには現在の`photo`が使われます。

<!--
Alternatively, you could pass both a photo's ID and a comment to the component:
-->

もしくは、`photo`のIDとコメントの両方をコンポーネントに渡すこともできます。

```app/templates/photo/index.hbs
<p>
  {{#link-to 'photo.comment' 5 primaryComment}}
    Main Comment for the Next Photo
  {{/link-to}}
</p>
```

<!--
In the above example, the model hook for `PhotoRoute` will run with `params.photo_id = 5`.  The `model` hook for
`CommentRoute` _won't_ run since you supplied a model object for the `comment` segment. The comment's id will
populate the url according to `CommentRoute`'s `serialize` hook.
-->

上記の例では、`PhotoRoute`のmodelフックは`params.photo_id=5`で実行されます。
`CommentRoute`の`model`フックは、`comment`セグメントに**モデルオブジェクトが渡されているため実行されません。**
コメントのIDは、`CommentRoute`の`serialize`フックに従ってURLに変換されます。

<!--
### Setting query-params
-->

### クエリーパラメーターの指定方法

<!--
The `query-params` helper can be used to set query params on a link:
-->

`query-params`ヘルパーを使って、クエリーパラメーターを指定できます。

```handlebars
// Explicitly set target query params
{{#link-to "posts" (query-params direction="asc")}}Sort{{/link-to}}

// Binding is also supported
{{#link-to "posts" (query-params direction=otherDirection)}}Sort{{/link-to}}
```

<!--
### Using link-to as an inline component
-->

### インラインコンポーネントとしてのlink-toを使う方法

<!--
In addition to being used as a block expression, the
[`link-to`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/get?anchor=link-to)
component can also be used in inline form by specifying the link text as the first
argument to the component:
-->

[`link-to`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/get?anchor=link-to)コンポーネントは、ブロック式として使用されるほか、リンクテキストをコンポーネントの第1引数として指定することで、インライン形式でも使用できます。

```handlebars
A link in {{#link-to "index"}}Block Expression Form{{/link-to}},
and a link in {{link-to "Inline Form" "index"}}.
```

<!--
The output of the above would be:
-->

上記の例は、以下のようになります。

```html
A link in <a href="/">Block Expression Form</a>,
and a link in <a href="/">Inline Form</a>.
```

<!--
### Adding additional attributes on a link
-->

### リンクに属性を追加する方法

<!--
When generating a link you might want to set additional attributes for it. You can do this with additional
arguments to the `link-to` component:
-->

リンクに属性を追加したければ、`link-to`コンポーネントに引数で追加します。

```handlebars
<p>
  {{link-to "Edit this photo" "photo.edit" photo class="btn btn-primary"}}
</p>
```

<!--
Many of the common HTML properties you would want to use like `class`, and `rel` will work. When
adding class names, Ember will also apply the standard `ember-view` and possibly `active` class names.
-->

`class`や`rel`のような共通のHTML属性の多くは動作します。
クラス名には、Emberは標準の`ember-view`が追加され、`active`も追加されたりします。

<!--
### Replacing history entries
-->

### ブラウザの履歴を置き換える方法

<!--
The default behavior for
[`link-to`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/get?anchor=link-to)
is to add entries to the browser's history when transitioning between the
routes. However, to replace the current entry in the browser's history you
can use the `replace=true` option:
-->

[`link-to`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/get?anchor=link-to)はデフォルトで、ルート間を移動する際にブラウザの履歴にエントリを追加します。
ブラウザの履歴にある現在のエントリを置き換えたい場合は、`replace=true`オプションを指定します。

```handlebars
<p>
  {{#link-to "photo.comment" 5 primaryComment replace=true}}
    Main Comment for the Next Photo
  {{/link-to}}
</p>
```
