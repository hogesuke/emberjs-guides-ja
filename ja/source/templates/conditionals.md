<!--
Statements like [`if`](https://www.emberjs.com/api/ember/release/classes/Ember.Templates.helpers/methods/if?anchor=if)
and [`unless`](https://www.emberjs.com/api/ember/release/classes/Ember.Templates.helpers/methods/if?anchor=unless)
are implemented as built-in helpers. Helpers can be invoked three ways, each
of which is illustrated below with conditionals.
-->

[`if`](https://www.emberjs.com/api/ember/release/classes/Ember.Templates.helpers/methods/if?anchor=if)と[`unless`](https://www.emberjs.com/api/ember/release/classes/Ember.Templates.helpers/methods/if?anchor=unless)のような文は、ビルトイン(組み込み)ヘルパーとして実装されています。
ヘルパーは3つの方法で呼び出すことができます
その3つ方法を、ifを例に説明します。

<!--
The first style of invocation is **inline invocation**. This looks similar to
displaying a property, but helpers accept arguments. For example:
-->

まず1つ目は、インライン呼び出しです。
これはプロパティの表示と似ていますが、ヘルパーは引数を取ることができます。

例:

```handlebars
<div>
  {{if isFast "zoooom" "putt-putt-putt"}}
</div>
```

<!--
[`{{if}}`](https://www.emberjs.com/api/ember/release/classes/Ember.Templates.helpers/methods/if?anchor=if)
in this case returns `"zoooom"` when `isFast` is true and
`"putt-putt-putt"` when `isFast` is false. Helpers invoked as inline expressions
render a single value, the same way that properties are a single value.
-->

上記の例の[`{{if}}`](https://www.emberjs.com/api/ember/release/classes/Ember.Templates.helpers/methods/if?anchor=if)は、`isFast`がtrueの時は`"zoooom"`を返し、`isFast`がfalseの時は`"putt-putt-putt"`を返します。
インライン式として呼び出されるヘルパーは、プロパティが単一の値であるのと同じように、単一の値を描画します。

<!--
Inline helpers don't need to be used inside HTML tags. They can also be used
inside attribute values:
-->

インラインヘルパーは、HTMLタグ内で使用する必要はありません。
属性値内でも使用できます。

```handlebars
<div class="is-car {{if isFast "zoooom" "putt-putt-putt"}}">
</div>
```

<!--
**Nested invocation** is another way to use a helper. Like inline helpers,
nested helpers generate and return a single value. For example, this template
only renders `"zoooom"` if both `isFast` and `isFueled` are true:
-->

2つ目の方法は、**ネスト**による呼び出しです。
インラインヘルパーと同様に、ネストされたヘルパーは単一の値を生成して返します。
以下のテンプレートは例では、`isFast`と`isFueled`の両方がtrueの場合、`"zoooom"`が描画されます。

```handlebars
<div>
  {{if isFast (if isFueled "zoooom")}}
</div>
```

<!--
The nested helper is called first returning `"zoooom"` only if `isFueled` is
true. Then the inline expression is called, rendering the nested helper's
value (`"zoooom"`) only if `isFast` is true.
-->

ネストされたヘルパーは、先に呼ばれ、`isFueled`がtrueの場合にのみ、`"zoooom"`を返します。
次に、`isFast`がtrueの場合にのみ、ネストされたヘルパーの値(`"zoooom"`)を描画するインライン式が呼び出されます。

<!--
The third form of helper usage is **block invocation**. Use block helpers
to render only part of a template. Block invocation of a helper can be
recognized by the `#` before the helper name, and the closing `{{/` double
curly brace at the end of the invocation.
-->

3つ目はブロック形式での呼び出しです。
ブロックヘルパーを使用して、テンプレートの一部のみを描画することができます。
ヘルパーのブロック呼び出しは、ヘルパー名の前の`#`と、呼び出し終了時の閉じ括弧`{{/`で感知されます。

<!--
For example, this template conditionally shows
properties on `person` only if that it is present:
-->

以下のテンプレートの例は、personが存在する場合のみ、人物の情報を表示しています。

```handlebars
{{#if person}}
  Welcome back, <b>{{person.firstName}} {{person.lastName}}</b>!
{{/if}}
```

<!--
[`{{if}}`](https://www.emberjs.com/api/ember/release/classes/Ember.Templates.helpers/methods/if?anchor=if)
checks for truthiness, which means all values except `false`,
`undefined`, `null`, `''`, `0`  or `[]` (i.e., any JavaScript falsy value or an
empty array).
-->

[`{{if}}`](https://www.emberjs.com/api/ember/release/classes/Ember.Templates.helpers/methods/if?anchor=if)は真(truthy)であるかを確認します。
真でない値(falsy)は、`false`、`undefined`、`null`、`''`、`0`または`[]`です。

<!--
If a value passed to `{{#if}}` evaluates to falsy, the `{{else}}` block
of that invocation is rendered:
-->

`{{#if}}`に渡された値がfalsyと評価された場合、その呼び出しの`{{else}}`ブロックが描画されます。

```handlebars
{{#if person}}
  Welcome back, <b>{{person.firstName}} {{person.lastName}}</b>!
{{else}}
  Please log in.
{{/if}}
```

<!--
`{{else}}` can chain helper invocation, the most common usecase for this being
`{{else if}}`:
-->

`{{else}}`はヘルパー呼び出しをチェーンすることができます。
最も一般的な使用例は`{{else if}}`です。

```handlebars
{{#if isAtWork}}
  Ship that code!
{{else if isReading}}
  You can finish War and Peace eventually...
{{/if}}
```

<!--
The inverse of `{{if}}` is
[`{{unless}}`](https://www.emberjs.com/api/ember/release/classes/Ember.Templates.helpers/methods/if?anchor=unless),
which can be used in the same three styles of invocation. For example, this
template only shows an amount due when the user has not paid:
-->

`{{if}}`の反対は[`{{unless}}`](https://www.emberjs.com/api/ember/release/classes/Ember.Templates.helpers/methods/if?anchor=unless)です。
ifと同じく3つの呼び出し方が使えます。
以下のテンプレートの例では、ユーザーが支払いを行わなかった場合に支払い額のみを表示します。

```handlebars
{{#unless hasPaid}}
  You owe: ${{total}}
{{/unless}}
```
