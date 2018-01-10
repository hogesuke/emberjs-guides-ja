<!--
To define a component, run:
-->

コンポーネントを定義するには、次のコマンドを実行します。

```shell
ember generate component my-component-name
```

<!--
Components must have at least one dash in their name. So `blog-post` is an acceptable
name, and so is `audio-player-controls`, but `post` is not. This prevents clashes with
current or future HTML element names, aligns Ember components with the W3C [Custom
Elements](https://dvcs.w3.org/hg/webcomponents/raw-file/tip/spec/custom/index.html)
spec, and ensures Ember detects the components automatically.
-->

コンポーネント名にはダッシュが少なくとも1つ必要です。
つまり、`blog-post`や`audio-player-controls`は使えますが、`post`は使えません。
これにより、現在または将来のHTML要素名との衝突を防ぎ、EmberコンポーネントをW3C [Custom
Elements](https://dvcs.w3.org/hg/webcomponents/raw-file/tip/spec/custom/index.html)仕様に揃え、Emberがコンポーネントを自動的に検出すること確実にします。

<!--
A sample component template could look like this:
-->

以下はコンポーネントテンプレートの例です。

```app/templates/components/blog-post.hbs
<article class="blog-post">
  <h1>{{title}}</h1>
  <p>{{yield}}</p>
  <p>Edit title: {{input type="text" value=title}}</p>
</article>
```

<!--
Given the above template, you can now use the `{{blog-post}}` component:
-->

上記のテンプレートがある場合、`{{blog-post}}`コンポーネントを使用できるようになます。

```app/templates/index.hbs
{{#each model as |post|}}
  {{#blog-post title=post.title}}
    {{post.body}}
  {{/blog-post}}
{{/each}}
```

<!--
Its model is populated in `model` hook in the route handler:
-->

modelはルートハンドラの`model`フックが返すデータです。

```app/routes/index.js
import Route from '@ember/routing/route';

export default Route.extend({
  model() {
    return this.get('store').findAll('post');
  }
});
```

<!--
Each component is backed by an element under the hood. By default, 
Ember will use a `<div>` element to contain your component's template.
To learn how to change the element Ember uses for your component, see
[Customizing a Component's
Element](../customizing-a-components-element).
-->

各コンポーネントは、フードの下にある要素によってサポートされています。
デフォルトでは、Emberは`<div>`要素を使用してコンポーネントのテンプレートを格納します。
Emberがコンポーネントに使用する要素を変更する方法については、[コンポーネントの要素のカスタマイズ](../customizing-a-components-element)を参照してください。

<!--
## Defining a Component Subclass
-->

## コンポーネントのサブクラスの定義

<!--
Often times, your components will just encapsulate certain snippets of
Handlebars templates that you find yourself using over and over. In
those cases, you do not need to write any JavaScript at all. Define
the Handlebars template as described above and use the component that is
created.
-->

しばしば、コンポーネントは、何度も繰り返し使用しているHandlebarsテンプレートをカプセル化します。
そのような場合は、JavaScriptをまったく書く必要はありません。
上記のようにハンドルバーテンプレートを定義し、作成されたコンポーネントを使用します。

<!--
If you need to customize the behavior of the component you'll
need to define a subclass of [`Component`](https://www.emberjs.com/api/ember/2.16/classes/Component). For example, you would
need a custom subclass if you wanted to change a component's element,
respond to actions from the component's template, or manually make
changes to the component's element using JavaScript.
-->

コンポーネントの動作をカスタマイズする必要がある場合は、[`Component`](https://www.emberjs.com/api/ember/2.16/classes/Component)のサブクラスを定義する必要があります。
例えば、コンポーネントの要素を変更したり、コンポーネントのテンプレートからのアクションに応答したり、JavaScriptを使用してコンポーネントの要素を手動で変更したりする場合は、サブクラスでカスタマイズします。

<!--
Ember knows which subclass powers a component based on its filename. For
example, if you have a component called `blog-post`, you would create a
file at `app/components/blog-post.js`. If your component was called
`audio-player-controls`, the file name would be at
`app/components/audio-player-controls.js`.
-->

Emberは、ファイル名に基づいてコンポーネントのサブクラスのファイルを特定します。
例えば、`blog-post`というコンポーネントの場合は、`app/components/blog-post.js`にファイルを作成します。
コンポーネントが`audio-player-controls`という名前の場合、ファイル名は`app/components/audio-player-controls.js`になります。

<!--
## Dynamically rendering a component
-->

## コンポーネントの動的レンダリング

<!--
The [`{{component}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/component?anchor=component) helper can be used to defer the selection of a component to
run time. The `{{my-component}}` syntax always renders the same component,
while using the `{{component}}` helper allows choosing a component to render on
the fly. This is useful in cases where you want to interact with different
external libraries depending on the data. Using the `{{component}}` helper would
allow you to keep different logic well separated.
-->

[`{{component}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/component?anchor=component)ヘルパーを使用して、コンポーネントの選択を実行時まで保留することができます。
`{{my-component}}`構文は常に同じコンポーネントを描画しますが、`{{component}}`ヘルパーを使用すると、実行時に描画するコンポーネントを選択できます。
これは、データに応じて異なる外部ライブラリとやり取りしたい場合に便利です。
`{{component}}`ヘルパーを使用すると、別のロジックを適切に分離しておくことができます。

<!--
The first parameter of the helper is the name of a component to render, as a
string. So `{{component 'blog-post'}}` is the same as using `{{blog-post}}`.
-->

ヘルパーの第1引数は、描画するコンポーネントの名前です。
つまり`{{component 'blog-post'}}`は`{{blog-post}}`と同じです。

<!--
The real value of [`{{component}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/component?anchor=component) comes from being able to dynamically pick
the component being rendered. Below is an example of using the helper as a
means of choosing different components for displaying different kinds of posts:
-->

[`{{component}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/component?anchor=component)の真の価値は、描画するコンポーネントを動的に選択できることにあります。
以下は、さまざまな種類の投稿を表示するためのさまざまなコンポーネントを選択する手段としてヘルパーを使用する例です。

```app/templates/components/foo-component.hbs
<h3>Hello from foo!</h3>
<p>{{post.body}}</p>
```

```app/templates/components/bar-component.hbs
<h3>Hello from bar!</h3>
<div>{{post.author}}</div>
```

```app/routes/index.js
import Route from '@ember/routing/route';

export default Route.extend({
  model() {
    return this.get('store').findAll('post');
  }
});
```

```app/templates/index.hbs
{{#each model as |post|}}
  {{!-- either foo-component or bar-component --}}
  {{component post.componentName post=post}}
{{/each}}
```

<!--
When the parameter passed to `{{component}}` evaluates to `null` or `undefined`,
the helper renders nothing. When the parameter changes, the currently rendered
component is destroyed and the new component is created and brought in.
-->

`{{component}}`に渡された引数が`null`または`undefined`と評価されると、ヘルパーは何も描画しません。
引数が変更されると、描画されているコンポーネントが破棄され、新たにコンポーネントが作成されて取り込まれます。

<!--
Picking different components to render in response to the data allows you to
have different template and behavior for each case. The `{{component}}` helper
is a powerful tool for improving code modularity.
-->

データに応じて描画するさまざまなコンポーネントを選択すると、それぞれのケースごとに異なるテンプレートと動作を持つことができます。
`{{component}}`ヘルパーは、コードのモジュール性を向上させる強力なツールです。
