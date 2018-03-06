<!--
To iterate over a list of items, use the
[`{{#each}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=each)
helper. The first argument to this helper is the array to be iterated, and
the value being iterated is yielded as a block param. Block params are only
available inside the block of their helper.
-->

リスト状のデータをイテレート(反復)するには、[`{{#each}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=each)ヘルパーを使用します。
このヘルパーの第1引数はイテレートされる配列であり、イテレートされる値はブロックに引数として渡されます。
ブロック変数は、ヘルパーのブロック内でのみ使用できます。

<!--
For example, this template iterates an array named `people` that contains
objects. Each item in the array is provided as the block param `person`.
-->

例えば、以下のテンプレートでは、`people`という名前のオブジェクトの配列をイテレートしています。
配列内の各データは、ブロックに`person`という引数で渡されています。


```handlebars
<ul>
  {{#each people as |person|}}
    <li>Hello, {{person.name}}!</li>
  {{/each}}
</ul>
```

<!--
Block params, like function arguments in JavaScript, are positional. `person`
is what each item is named in the above template, but `human` would work just
as well.
-->

ブロック変数は、JavaScriptの関数引数のように位置パラメーターです。
上記のテンプレートでは、`person`という名前が付けられていますが、`human`でも同様に機能します。

<!--
The template inside of the `{{#each}}` block will be repeated once for
each item in the array, with the each item set to the `person` block param.
-->

`{{#each}}`ブロック内のテンプレートは、配列内の各項目に対して1回ずつ繰り返され、各項目はブロック変数として`person`という名前で渡されています。

<!--
Given an input array like:
-->

`people`が次のようなデータの場合、

```js
[
  { name: 'Yehuda' },
  { name: 'Tom' },
  { name: 'Trek' }
]
```

<!--
The above template will render HTML like this:
-->

以下のHTMLが描画されます。


```html
<ul>
  <li>Hello, Yehuda!</li>
  <li>Hello, Tom!</li>
  <li>Hello, Trek!</li>
</ul>
```

<!--
Like other helpers, the `{{#each}}` helper is bound.  If a new item is added to
or removed from the iterated array, the DOM will be updated without having to
write any additional code. That said, Ember requires that you use [special
methods](../../object-model/enumerables/#toc_use-of-observable-methods-and-properties)
to update bound arrays. Also be aware that [using the `key` option with an each
helper](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=each)
can improve re-render performance when an array is replaced with another
containing similar items.
-->

他のヘルパーと同様、`{{#each}}`ヘルパーはバインドされています。
配列にデータが追加または削除された際に、DOMが自動的に更新されます。
ですが、Emberでは、バインドされた配列を変更するには、[専用のメソッド](../../object-model/enumerables/#toc_use-of-observable-methods-and-properties)を使う必要があります。
また、[eachヘルパーで`key`オプションを使用する](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=each)と、配列が似た項目を含む別のものに置き換える時の再描画のパフォーマンスが向上することを覚えておいてください。


<!--
### Accessing an item's `index`
-->

### インデックスを使う方法

<!--
During iteration, the index of each item in the array is provided as a second
block param. Block params are space-separated, without commas. For example:
-->

イテレートの際、配列内の各項目のインデックスがブロックの第2引数に渡されます。
ブロック変数はコンマではなくスペースで区切ります。

例:

```handlebars
<ul>
  {{#each people as |person index|}}
    <li>Hello, {{person.name}}! You're number {{index}} in line</li>
  {{/each}}
</ul>
```

### 空のリストとelse

<!--
The [`{{#each}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=each)
helper can have a corresponding `{{else}}`. The contents of this block will
render if the array passed to `{{#each}}` is empty:
-->

`{{#each}}`ヘルパー内には`{{else}}`を入れることができます。
`{{#each}}`に渡された配列が空の場合は、elseブロックの内容が表示されます。

```handlebars
{{#each people as |person|}}
  Hello, {{person.name}}!
{{else}}
  Sorry, nobody is here.
{{/each}}
```
