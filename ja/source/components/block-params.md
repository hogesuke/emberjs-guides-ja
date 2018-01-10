*訳注:ブロック自体を引数に渡したり引数に取ることをブロック引数と訳しています。ブロック内で使う引数のことはブロック変数と訳しています。この２つはどちらもブロックパラメーター(block parameter)という同じ名前で呼ばれることがありますが、混乱を避けるために敢えてそのように訳しています。*

<!--
Components can have properties passed in ([Passing Properties to a Component](../passing-properties-to-a-component/)),
but they can also return output to be used in a block expression.
-->

コンポーネントはプロパティ([コンポーネントへのプロパティの受け渡し](../passing-properties-to-a-component/))だけでなく、ブロックも渡すこともできます。

<!--
### Return values from a component with `yield`
-->

### `yield`でコンポーネントから値を返す

```app/templates/index.hbs
{{blog-post post=model}}
```

```app/templates/components/blog-post.hbs
{{yield post.title post.body post.author}}
```

<!--
Here an entire blog post model is being passed to the component as a single component property.
In turn the component is returning values using `yield`.
In this case the yielded values are pulled from the post being passed in
but anything that the component has access to can be yielded, such as an internal property or something from a service.
-->

上記の例では、ブログ記事のモデル全体が１つのコンポーネントプロパティにまとめて渡されています。
その次の例では、コンポーネント側で`yield`を使っていくつか値を返しています。
この例ではyieldで返している値はコンポーネントに渡されたpostから取り出すことになりますが、内部プロパティやサービスなどのコンポーネントがアクセスできるものは全てyieldで渡すことができます。

<!--
### Consuming yielded values with block params
-->

### ブロック内でyieldで渡された値を使う

<!--
The block expression can then use block params to bind names to any yielded values for use in the block.
This allows for template customization when using a component,
where the markup is provided by the consuming template,
but any event handling behavior implemented in the component is retained such as `click()` handlers.
-->

ブロック式では、yieldで渡された値をブロック変数として名前を付けて使うことができます。
これにより、`click()`ハンドラなどのコンポーネントで実装されたイベント処理動作はすべて保ったまま、
コンポーネントにマークアップをブロックで渡してコンポーネントの内側のテンプレートを変更することができるようになります。

```app/templates/index.hbs
{{#blog-post post=model as |title body author|}}
  <h2>{{title}}</h2>
  <p class="author">by {{author}}</p>
  <p class="post-body">{{body}}</p>
{{/blog-post}}
```

<!--
The names are bound in the order that they are passed to `yield` in the component template.
-->

ブロック変数はコンポーネントのテンプレートで`yeild`に渡した順序でバインドされます。

<!--
### Supporting both block and non-block component usage in one template
-->

### ブロックと非ブロックコンポーネントの両方をサポートする

<!--
It is possible to support both block and non-block usage of a component from a single component template
using the `has-block` helper.
-->

`has-block`ヘルパーを使用して、1つのコンポーネントテンプレートでブロックを使った時と使っていない時の両方をサポートすることができます。

```app/templates/components/blog-post.hbs
{{#if (has-block)}}
  {{yield post.title post.body post.author}}  
{{else}}
  <h1>{{post.title}}</h1>
  <p class="author">Authored by {{post.author}}</p>
  <p>{{post.body}}</p>
{{/if}}
```

<!--
This has the effect of providing a default template when using a component in the non-block form
but providing yielded values for use with block params when using a block expression.
-->

これにより、デフォルトで非ブロック形式を使え、さらにブロック形式も使え、
かつブロック内ではブロック変数が使えるという効果があります。
