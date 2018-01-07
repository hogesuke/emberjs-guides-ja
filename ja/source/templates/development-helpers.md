<!--
## Development Helpers
-->

## 開発用ヘルパー

<!--
Handlebars and Ember come with a few helpers that can make developing your
templates a bit easier. These helpers make it simple to output variables into
your browser's console, or activate the debugger from your templates.
-->

ハンドルバーとEmberには、テンプレートの開発をちょっと簡単にできるようにいくつかのヘルパーが付属しています。
これから紹介するヘルパーを使うことで、ブラウザのコンソールに変数を出力したり、テンプレートからデバッガを起動したりするが簡単になります。

<!--
### Logging
-->

### ログ

<!--
The [`{{log}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=log) helper makes it easy to output variables or expressions in
 the
current rendering context into your browser's console:
-->

[`{{log}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=log)ヘルパーを使うと、コンテキスト内の変数や式をブラウザのコンソールに簡単に出力できます。

```handlebars
{{log 'Name is:' name}}
```
<!--
The `{{log}}` helper also accepts primitive types such as strings or numbers.
-->

`{{log}}`ヘルパーは、文字列や数値などのプリミティブ型も受け入れます。

<!--
### Adding a breakpoint
-->

### ブレークポイントの追加

<!--
The [``{{debugger}}``](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=debugger) helper provides a handlebars equivalent to JavaScript's
`debugger` keyword.  It will halt execution inside the debugger helper and give
you the ability to inspect the current rendering context:
-->

[`{{debugger}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=debugger)ヘルパーは、HandlebarsでもJavaScriptの`debugger`キーワードに相当します。
debuggerはヘルパー内で実行を停止し、コンテキストを検査する機能を提供します。

```handlebars
{{debugger}}
```

<!--
When using the debugger helper you will have access to a `get` function. This
function retrieves values available in the context of the template.
For example, if you're wondering why a value `{{foo}}` isn't rendering as
expected within a template, you could place a `{{debugger}}` statement and,
when the `debugger;` breakpoint is hit, you can attempt to retrieve this value:
-->

debuggerヘルパーを使用する際、`get`関数が使えます。
この関数は、テンプレートのコンテキストで使用可能な値を取得します。
例えば、`{{foo}}`がテンプレートに描画されない理由を知りたい場合、`{{debugger}}`を置いて、そのブレークポイントがヒットした時に、以下のようにしてfooを調べることができます。

```javascript
> get('foo')
```

<!--
`get` is also aware of keywords. So in this situation:
-->

`get`はキーワードも認識しています。
したがって、以下の場合、

```handlebars
{{#each items as |item|}}
  {{debugger}}
{{/each}}
```

<!--
You'll be able to get values from the current item:
-->

`item`を取得できます。

```javascript
> get('item.name')
```

<!--
You can also access the context of the view to make sure it is the object that
you expect:
-->

また、ビューのコンテキストにアクセスして、ビューが期待するオブジェクトであることを確認することもできます。

```javascript
> context
```
