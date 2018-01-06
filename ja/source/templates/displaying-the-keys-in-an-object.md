<!--
If you need to display all of the keys or values of a JavaScript object in your template,
you can use the [`{{#each-in}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=each-in) helper:
-->

テンプレートにJavaScriptオブジェクトのすべてのキーまたは値を表示したい時は、[`{{#each-in}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=each-in)ヘルパーを使います。

```/app/components/store-categories.js
import Component from '@ember/component';

export default Component.extend({
  willRender() {
    // Set the "categories" property to a JavaScript object
    // with the category name as the key and the value a list
    // of products.
    this.set('categories', {
      'Bourbons': ['Bulleit', 'Four Roses', 'Woodford Reserve'],
      'Ryes': ['WhistlePig', 'High West']
    });
  }
});
```

```/app/templates/components/store-categories.hbs
<ul>
  {{#each-in categories as |category products|}}
    <li>{{category}}
      <ol>
        {{#each products as |product|}}
          <li>{{product}}</li>
        {{/each}}
      </ol>
    </li>
  {{/each-in}}
</ul>
```

<!--
The template inside of the `{{#each-in}}` block is repeated once for each key in the passed object.
The first block parameter (`category` in the above example) is the key for this iteration,
while the second block parameter (`products`) is the actual value of that key.
-->

`{{#each-in}}`ブロック内のテンプレートは、渡されたオブジェクトの各キー毎に1回づつ繰り返されます。
第1ブロックパラメーター(上記の例では`category`)はキーで、第2ブロックパラメーター(`products`)はそのキーの値です。

<!--
The above example will print a list like this:
-->

上記の例では、次のようなリストが出力されます。

```html
<ul>
  <li>Bourbons
    <ol>
      <li>Bulleit</li>
      <li>Four Roses</li>
      <li>Woodford Reserve</li>
    </ol>
  </li>
  <li>Ryes
    <ol>
      <li>WhistlePig</li>
      <li>High West</li>
    </ol>
  </li>
</ul>
```

<!--
### Ordering
-->

### キーの順序と並べ替え

<!--
An object's keys will be listed in the same order as the array returned from calling `Object.keys` on that object.
If you want a different sort order, you should use `Object.keys` to get an array, sort that array with the built-in JavaScript tools,
and use the [`{{#each}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=each-in) helper instead.
-->

オブジェクトのキーの順序は、`Object.keys()`の結果と同じです。
キーを並べ替えたい場合は、`Object.keys`で配列を取得し、JavaScriptの組み込み関数などをを使用してその配列をソートし、[`{{#each}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=each-in)ヘルパーを使用します。

<!--
### Empty Lists
-->

### 空のリストとelse

<!--
The [`{{#each-in}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=each-in)
helper can have a matching `{{else}}`.
The contents of this block will render if the object is empty, null, or undefined:
-->

[`{{#each-in}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=each-in)ヘルパーは`{{else}}`が使えます。
else内のブロックは、オブジェクトが空、`null`、または`undefined`の場合に表示されます。

```handlebars
{{#each-in people as |name person|}}
  Hello, {{name}}! You are {{person.age}} years old.
{{else}}
  Sorry, nobody is here.
{{/each-in}}
```
