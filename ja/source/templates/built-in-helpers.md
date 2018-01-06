<!--
## Built-in Helpers
-->

## ビルトインヘルパー

<!--
In the last section you learned how to write a helper.
A helper is usually a simple function that can be
used in any template.
Ember comes with a few helpers that can make developing your
templates a bit easier.
These helpers can allow you to be more dynamic in
passing data to another helper or component.
-->

最後のセクションでは、ヘルパーを書く方法を学びました。 ヘルパーは通常、どのテンプレートでも使用できる単純な関数です。 Emberにはいくつかのヘルパーが用意されているので、テンプレートの開発を簡単にすることができます。 これらのヘルパーを使用すると、データを別のヘルパーやコンポーネントに渡す際の動的性を高めることができます。

<!--
### Using a helper to get a property dynamically
-->

### ヘルパーを使ってプロパティを動的に取得する

<!--
The [`{{get}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/get?anchor=get) helper makes it easy to dynamically send the value of a
variable to another helper or component.
This can be useful if you want
to output one of several values based on the result of a computed property.
-->

[`{{get}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/get?anchor=get)ヘルパーを使うと、変数の値を他のヘルパーやコンポーネントに動的に簡単に送ることができます。
これは、計算されたプロパティの結果に基づいていくつかの値のいずれかを出力する場合に便利です。

```handlebars
{{get address part}}
```

<!--
if the `part` computed property returns "zip", this will display the result of
`this.get('address.zip')`. If it returns "city", you get `this.get('address.city')`.
-->

算出プロパティ`part`が "zip"を返した場合、`this.get('address.zip')`の結果が表示されます。
"city"を返すと`this.get('address.city')`が返されます。

<!--
### Nesting built-in helpers
-->

### ビルトインヘルパーのネスト

<!--
In the last section it was discussed that helpers can be nested.
This can be combined with these sorts of dynamic helpers.
For example, the [`{{concat}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/get?anchor=concat) helper makes it easy to dynamically send
a number of parameters to a component or helper as a single parameter in the
format of a concatenated string.
-->

前のセクションでは、ヘルパーを入れ子にできることを説明しました。
入れ子により動的ヘルパーと組み合わせることができます。
例えば、[`{{concat}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/get?anchor=concat)ヘルパーを使用すると、多数のパラメータを動的に文字列として連結し、一つのパラメーターにして、コンポーネントまたはヘルパーに渡すことが簡単にできます。

```handlebars
{{get "foo" (concat "item" index)}}
```

<!--
This will display the result of `this.get('foo.item1')` when index is 1,
and `this.get('foo.item2')` when index is 2, etc.
-->

上記の例では、indexが1の時は`this.get('foo.item1')`の結果を、indexが2の時は`this.get('foo.item2')`の結果を表示します。
