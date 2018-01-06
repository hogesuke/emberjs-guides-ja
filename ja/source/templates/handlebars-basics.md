<!--
Ember uses the [Handlebars templating library](http://www.handlebarsjs.com)
to power your app's user interface. Handlebars templates contain static HTML and dynamic content inside Handlebars expressions, which are invoked with double curly braces: `{{}}`.
-->

Emberはテンプレートライブラリの[Handlebars](http://www.handlebarsjs.com)を使用しています。
Handlebarsテンプレートには、Handlebarsの式である`{{}}`(二重中括弧)で囲まれた動的コンテンツと静的HTMLとを含むことができます。

<!--
Dynamic content inside a Handlebars expression is rendered with data-binding. This means if you update a property, your usage of that property in a template will be automatically updated to the latest value.
-->

Handlebars式内の動的コンテンツは、データにバインドされて描画されます。
つまり、プロパティを更新すると、テンプレート内でそのプロパティを使っているところが自動的に最新の値に更新されます。

<!--
### Displaying Properties
-->

### プロパティを表示する

<!--
Templates are backed with a context. A context is an object from which
Handlebars expressions read their properties. In Ember this is often a component. For templates rendered by a route (like `application.hbs`), the context is a controller.
-->

テンプレートにはコンテキストがあります。
コンテキストとは、Handlebars式がプロパティを読み取るオブジェクトのことです。
Emberでは、コンポーネントがコンテキストになることが多々あります。
ルートによって描画されたテンプレート(`application.hbs`など)の場合、コンテキストはコントローラです。

<!--
For example, this `application.hbs` template will render a first and last name:
-->

例えば、以下の`application.hbs`テンプレートで氏名を表示する場合、

<!--
```app/templates/application.hbs
Hello, <strong>{{firstName}} {{lastName}}</strong>!
```
-->

```app/templates/application.hbs
<strong>{{lastName}}{{firstName}}</strong>さん、こんにちは!
```

<!--
The `firstName` and `lastName` properties are read from the
context (the application controller in this case), and rendered inside the
`<strong>` HTML tag.
-->

`firstName`および`lastName`プロパティはコンテキスト(この場合はアプリケーションコントローラ)から読み込まれ、
`<strong>`HTMLタグ内に描画されます。

<!--
To provide a `firstName` and `lastName` to the above template, properties
must be added to the application controller. If you are following along with
an Ember CLI application, you may need to create this file:
-->

上記のテンプレートに`firstName`と`lastName`の値を指定するには、
アプリケーションコントローラにプロパティを追加する必要があります。
Ember CLIでアプリケーションを開発している場合は、以下のファイルが無ければ作成する必要があります。

```app/controllers/application.js
import Controller from '@ember/controller';

export default Controller.extend({
  firstName: 'Trek',
  lastName: 'Glowacki'
});
```

<!--
The above template and controller render as the following HTML:
-->

上記のテンプレートとコントローラを描画すると以下のHTMになります。

```html
Hello, <strong>Trek Glowacki</strong>!
```

<!--
Remember that `{{firstName}}` and `{{lastName}}` are bound data. That means
if the value of one of those properties changes, the DOM will be updated
automatically.
-->

`{{firstName}}`と`{{lastName}}`はバインドされたデータであることに注意してください。
つまり、これらのプロパティの値が変更されると、DOMは自動的に更新されます。

<!--
As an application grows in size, it will have many templates backed by
controllers and components.
-->

アプリケーションのサイズが大きくなるにつれて、コントローラかコンポーネントをコンテキストに持つテンプレートも多くなります。

<!--
### Helpers
-->

### ヘルパー

<!--
Ember gives you the ability to [write your own helpers](../writing-helpers/), to bring a minimum of logic into Ember templating.
-->

Emberでは、[独自のヘルパーを書く](../writing-helpers/),ことができ、テンプレート内のロジックを最低限に抑えることができます。

<!--
For example, let's say you would like the ability to add a few numbers together, without needing to define a computed property everywhere you would like to do so.
-->

例えば、算出プロパティを定義しないで、どこでも数値を足せるようにするとしましょう。

```app/helpers/sum.js
import { helper } from "@ember/component/helper"

export function sum(params) {
  return params.reduce((a, b) => {
    return a + b;
  });
};

export default helper(sum);
```

<!--
The above code will allow you invoke the `sum()` function as a `{{sum}}` handlebars "helper" in your templates:
-->

上記のコードでは、テンプレート内のHandlesbarsバーのヘルパー`{{sum}}`として`sum()`関数を呼び出すことができます：

```html
<p>Total: {{sum 1 2 3}}</p>
```

<!--
This helper will output a value of `6`.
-->

このヘルパーは値`6`を出力します。

<!--
Ember ships with several built-in helpers, which you will learn more about in the following guides.
-->

Emberにはいくつかのビルトイン(組み込み)ヘルパーが同梱されています。
ビルトインヘルパーについては、次のページで詳しく説明しています。

<!--
#### Nested Helpers
-->

#### ヘルパー内でヘルパーを呼ぶ方法

<!--
Helpers have the ability to be nested within other helper invocations and also component invocations.
-->

ヘルパーは、他のヘルパー呼び出しやコンポーネント呼び出しの中にネストすることができます。

<!--
This gives you the flexibility to compute a value _before_ it is passed in as an argument or an attribute of another.
-->

これにより、値が別の引数または属性として渡される前に値を計算する柔軟性が得られます。

<!--
It is not possible to nest curly braces `{{}}`, so the correct way to nest a helper is by using parentheses `()`:
-->

中括弧`{{}}`をネストすることはできません。
ヘルパーを入れ子にするには、括弧`()`を使います。

```html
{{sum (multiply 2 4) 2}}
```

<!--
In this example, we are using a helper to multiply `2` and `4` _before_ passing the value into `{{sum}}`.
-->

この例では、値を`{{sum}}`に渡す前にヘルパーを使用して`2`と`4`を乗算しています。

<!--
Thus, the output of these combined helpers is `10`.
-->

したがって、結果は`10`になります。

<!--
As you move forward with these template guides, keep in mind that a helper can be used anywhere a normal value can be used.
-->

テンプレートのガイドを読み進めていく間は、通常の値を使用できる場所ならどこでもヘルパーを使用できることを覚えておいてください。

<!--
Thus, many of Ember's built-in helpers (as well as your custom helpers) can be used in nested form.
-->

それと、Emberのビルトインヘルパー(とカスタムヘルパー)の多くは、ネストされた形式で使用できることもです。
