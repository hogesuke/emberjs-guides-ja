<!--
Components are isolated from their surroundings, so any data that the component
needs has to be passed in.
-->

コンポーネントはその周囲から分離されているため、コンポーネントが必要とするデータはすべて渡す必要があります。

<!--
For example, imagine you have a `blog-post` component that is used to
display a blog post:
-->

例えば、ブログ記事の表示に使用する`blog-post`コンポーネントがあるとして、次のテンプレートと、

```app/templates/components/blog-post.hbs
<article class="blog-post">
  <h1>{{title}}</h1>
  <p>{{body}}</p>
</article>
```

<!--
Now imagine we have the following template and route:
-->

次のルートがあるとします。

```app/routes/index.js
import Route from '@ember/routing/route';

export default Route.extend({
  model() {
    return this.get('store').findAll('post');
  }
});
```

<!--
If we tried to use the component like this:
-->

以下のようにこのコンポーネントを使用すると、

```app/templates/index.hbs
{{#each model as |post|}}
  {{blog-post}}
{{/each}}
```

<!--
The following HTML would be rendered:
-->

次のHTMLが表示されます。

```html
<article class="blog-post">
  <h1></h1>
  <p></p>
</article>
```

<!--
In order to make a property available to a component, you must pass it
in like this:
-->

コンポーネントでプロパティを使用できるようにするには、以下のようにプロパティを渡す必要があります。

```app/templates/index.hbs
{{#each model as |post|}}
  {{blog-post title=post.title body=post.body}}
{{/each}}
```

<!--
It is important to note that these properties stay in sync (technically
known as being "bound"). That is, if the value of `componentProperty`
changes in the component, `outerProperty` will be updated to reflect that
change. The reverse is true as well.
-->

プロパティは同期するというのを把握しておくことが重要です(技術的には「バインドされている」と言ったりします)。
つまり、`componentProperty`の値がコンポーネント内で変更されると、`outerProperty`にもその変更が反映され更新されます。
その逆も然りです。

<!--
## Positional Params
-->

## 位置パラメーター

<!--
In addition to passing parameters in by name, you can pass them in by position.
In other words, you can invoke the above component example like this:
-->

名前でパラメーターを渡すことに加えて、位置によってパラメータを渡すことができます。
つまり、以下のように上記のコンポーネントの例を呼び出すことができます。

```app/templates/index.hbs
{{#each model as |post|}}
  {{blog-post post.title post.body}}
{{/each}}
```

<!--
To set the component up to receive parameters this way, you need to
set the [`positionalParams`](https://www.emberjs.com/api/ember/2.16/classes/Component/properties/positionalParams?anchor=positionalParams) attribute in your component class.
-->

このようにパラメーターを受け取れるようにするにはコンポーネントクラスで[`positionalParams`](https://www.emberjs.com/api/ember/2.16/classes/Component/properties/positionalParams?anchor=positionalParams)属性を設定する必要があります。

```app/components/blog-post.js
import Component from '@ember/component';

export default Component.extend({}).reopenClass({
  positionalParams: ['title', 'body']
});
```

<!--
Then you can use the attributes in the component exactly as if they had been
passed in like `{{blog-post title=post.title body=post.body}}`.
-->

すると、`{{blog-post title=post.title body=post.body}}`のように渡したように、コンポーネント内で各プロパティを使用することができます。

<!--
Notice that the `positionalParams` property is added to the class as a
static variable via `reopenClass`. Positional params are always declared on
the component class and cannot be changed while an application runs.
-->

`locationsParams`プロパティは`reopenClass`を介して静的変数としてクラスに追加します。
位置パラメーターは常にコンポーネントクラスで宣言し、アプリケーションの実行中は変更できません。

<!--
Alternatively, you can accept an arbitrary number of parameters by
setting `positionalParams` to a string, e.g. `positionalParams: 'params'`. This
will allow you to access those params as an array like so:
-->

`positionParams`に文字列に設定すると、任意の数のパラメータを受け入れることができます。(例: `positionalParams: 'params'`-
これにより、パラメーターに配列としてアクセスすることができます。

```app/components/blog-post.js
import Component from '@ember/component';
import { computed } from '@ember/object';

export default Component.extend({
  title: computed('params.[]', function(){
    return this.get('params')[0];
  }),
  body: computed('params.[]', function(){
    return this.get('params')[1];
  })
}).reopenClass({
  positionalParams: 'params'
});
```
