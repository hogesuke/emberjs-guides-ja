<!--
Sometimes, you may want to define a component that wraps content provided by other templates.
-->

場合によっては、他のテンプレートのコンテンツをラップするコンポーネントを定義したい時があります。

<!--
For example, imagine we are building a `blog-post` component that we can use in our application to display a blog post:
-->

例えば、アプリケーションでブログ記事の表示に使う`blog-post`コンポーネントを作成するとします。

```app/templates/components/blog-post.hbs
<h1>{{title}}</h1>
<div class="body">{{body}}</div>
```

<!--
Now, we can use the `{{blog-post}}` component and pass it properties in another template:
-->

これで`{{blog-post}}`コンポーネントを使用して、別のテンプレートのプロパティを渡すことができます。

```handlebars
{{blog-post title=title body=body}}
```

<!--
See [Passing Properties to a Component](../passing-properties-to-a-component/) for more.
-->

詳細については、[コンポーネントへのプロパティの受け渡し](../passing-properties-to-a-component/)を参照してください。

<!--
In this case, the content we wanted to display came from the model.
But what if we want the developer using our component to be able to provide custom HTML content?
-->

この場合、表示したいコンテンツはモデルにあるものです。
しかし、このコンポーネント内に任意のHTMLコンテンツを渡せるようにするにはどうすればよいでしょうか？

<!--
In addition to the simple form you've learned so far,
components also support being used in **block form**.
In block form, components can be passed a Handlebars template that is rendered inside the component's template wherever the `{{yield}}` expression appears.
-->

これまでに学んだシンプルな形式に加えて、コンポーネントは**ブロック形式**での使用もサポートしています。
ブロック形式では、Handlebarsテンプレートをコンポーネントに渡すことができ、コンポーネントのテンプレート内の`{{yield}}`式があるところで描画します。

<!--
To use the block form, add a `#` character to the beginning of the component name,
then make sure to add a closing tag.
-->

ブロック形式を使用するには、コンポーネント名の先頭に`#`文字を追加し、必ず閉じタグを追加します。

<!--
See the Handlebars documentation on [block expressions](http://handlebarsjs.com/#block-expressions) for more.
-->

詳細については、[ブロック式](http://handlebarsjs.com/#block-expressions)に関するHandlebarsのドキュメントを参照してください。

<!--
In that case, we can use the `{{blog-post}}` component in **block form** and tell Ember where the block content should be rendered using the `{{yield}}` helper.
To update the example above, we'll first change the component's template:
-->

この場合、`{{blog-post}}`コンポーネントをブロック形式で使用して、`{{yield}}`ヘルパーを使用してコンテンツを描画する場所をEmberに伝えることができます。
まず上記の例のコンポーネントのテンプレートを変更します。


```app/templates/components/blog-post.hbs
<h1>{{title}}</h1>
<div class="body">{{yield}}</div>
```

<!--
You can see that we've replaced `{{body}}` with `{{yield}}`.
This tells Ember that this content will be provided when the component is used.
-->

`{{body}}`を`{{yield}}`に置き換えました。
これにより、コンポーネントが使用される時にコンテンツが渡されることがEmberに伝わります。

<!--
Next, we'll update the template using the component to use the block form:
-->

次に、ブロック形式でこのコンポーネントを使用するようテンプレートを更新します。

```app/templates/index.hbs
{{#blog-post title=title}}
  <p class="author">by {{author}}</p>
  {{body}}
{{/blog-post}}
```

<!--
It's important to note that the template scope inside the component block is the same as outside.
If a property is available in the template outside the component, it is also available inside the component block.
-->

コンポーネントブロック内のテンプレートスコープは外側と同じであることに注意することが重要です。
プロパティがコンポーネントの外のテンプレートで使用可能な場合は、コンポーネントブロック内でも使用できます。

<!--
## Sharing Component Data with its Wrapped Content
-->

## ラップされたコンテンツでのコンポーネントデータの共有

<!--
There is also a way to share data within your blog post component with the content it is wrapping.
In our blog post component we want to provide a way for the user to configure what type of style they want to write their post in.
We will give them the option to specify either `markdown-style` or `html-style`.
-->

また、ブログ記事コンポーネント内のデータをラッピングしているコンテンツと共有する方法もあります。
ブログ記事コンポーネントでは、ユーザーが自分の記事の書式を設定できるようにしたいとします。
`markdown-style`または`html-style`のいずれかを選択できるようにします。

```app/templates/index.hbs
{{#blog-post editStyle="markdown-style"}}
  <p class="author">by {{author}}</p>
  {{body}}
{{/blog-post}}
```

<!--
Supporting different editing styles will require different body components to provide special validation and highlighting.
To load a different body component based on editing style,
you can yield the component using the [`component helper`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/component?anchor=component) and [`hash helper`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/hash?anchor=hash). 
Here, the appropriate component is assigned to a hash using nested helpers and yielded to the template.
Notice `editStyle` being used as an argument to the component helper.
-->

異なる編集スタイルをサポートするには、特別なバリデーションとハイライトを提供するために異なるボディコンポーネントが必要です。
編集スタイルに基づいて別のボディコンポーネントをロードするには、[`componentヘルパー`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/component?anchor=component)と[`hashヘルパー`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/hash?anchor=hash)コンポーネントヘルパーとハッシュヘルパーを使用してコンポーネントを渡します。
ここでは、ネストされたヘルパーを使用して適切なコンポーネントがハッシュに割り当てられ、テンプレートに渡されます。
`editStyle`はcomponentヘルパーの引数として使用されています。

```app/templates/components/blog-post.hbs
<h2>{{title}}</h2>
<div class="body">{{yield (hash body=(component editStyle))}}</div>
```

<!--
Once yielded, the data can be accessed by the wrapped content by referencing the `post` variable.
Now a component called `markdown-style` will be rendered in `{{post.body}}`.
-->

データが取得されると、`post`変数を参照することによって、ラップされたコンテンツがデータにアクセスできます。
これで、`markdown-style`というコンポーネントが`{{post.body}}`に描画されます。

```app/templates/index.hbs
{{#blog-post editStyle="markdown-style" postData=myText as |post|}}
  <p class="author">by {{author}}</p>
  {{post.body}}
{{/blog-post}}
```

<!--
Finally, we need to share `myText` with the body in order to have it display.
To pass the blog text to the body component, we'll add a `postData` argument to the component helper.
-->

最後に、myTextを表示させるために`myText`をbodyと共有する必要があります。
ブログのテキストをbodyコンポーネントに渡すために、componentヘルパーに`postData`引数を追加します。

```app/templates/components/blog-post.hbs
<h2>{{title}}</h2>
<div class="body">
  {{yield (hash
    body=(component editStyle postData=postData)
  )}}
</div>
```

<!--
At this point, our block content has access to everything it needs to render,
via the wrapping `blog-post` component's template helpers.
-->

この時点で、ブロックコンテンツは、ラッピングしている`blog-post`コンポーネントのテンプレートヘルパーを介して、描画に必要なすべてのものにアクセスできます。

<!--
Additionally, since the component isn't instantiated until the block content is rendered,
we can add arguments within the block.
In this case we'll add a text style option which will dictate the style of the body text we want in our post.
When `{{post.body}}` is instantiated, it will have both the `editStyle` and `postData` given by its wrapping component,
as well as the `bodyStyle` declared in the template.
-->

さらに、ブロックコンテンツが描画されるまで、コンポーネントはインスタンス化されないため、ブロック内に引数を追加できます。
この場合、本文のスタイルを指定するテキストスタイルオプションを追加します。
`{{post.body}}`がインスタンス化されると、ラッピングコンポーネントによって与えられた`editStyle`と`postData`と、テンプレートで宣言された`bodyStyle`を持つようになります。

```app/templates/index.hbs
{{#blog-post editStyle="markdown-style" postData=myText as |post|}}
  <p class="author">by {{author}}</p>
  {{post.body bodyStyle="compact-style"}}
{{/blog-post}}
```

<!--
Components built this way are commonly referred to as "Contextual Components",
allowing inner components to be wrapped within the context of outer components without breaking encapsulation.
-->

このように構築されたコンポーネントは一般に「コンテキストコンポーネント」と呼ばれ、カプセル化を壊すことなく内部コンポーネントを外部コンポーネントのコンテキスト内でラップすることができます。
