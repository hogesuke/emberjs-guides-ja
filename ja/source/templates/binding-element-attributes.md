<!--
In addition to normal text, you may also want to have your templates
contain HTML elements whose attributes are bound to the controller.
-->

テキストと同じように、HTML要素の属性とコントローラのプロパティをバインドすることができます。

<!--
For example, imagine your controller has a property that contains a URL
to an image:
-->

例えば、コントローラの`logoURL`プロパティに画像のURLが含まれているとします。

```handlebars
<div id="logo">
  <img src={{logoUrl}} alt="Logo">
</div>
```

<!--
This generates the following HTML:
-->

これにより、次のHTMLが生成されます。

```html
<div id="logo">
  <img src="http://www.example.com/images/logo.png" alt="Logo">
</div>
```

<!--
If you use data binding with a Boolean value, it will add or remove
the specified attribute. For example, given this template:
-->

ブール値でデータバインディングすると、指定した属性が追加または削除されます。 たとえば、次のテンプレートを指定します。

```handlebars
<input type="checkbox" disabled={{isAdministrator}}>
```

<!--
If `isAdministrator` is `true`, Handlebars will produce the following
HTML element:
-->

`isAdministrator`が`true`の場合、以下のHTMLが出力されます。

```html
<input type="checkbox" disabled>
```

<!--
If `isAdministrator` is `false`, Handlebars will produce the following:
-->

`isAdministrator`が`false`の場合、以下のHTMLが出力されます。

```html
<input type="checkbox">
```

<!--
### Adding Data Attributes
-->

### data属性を追加する

<!--
By default, helpers and components do not accept *data attributes*. For example
-->

デフォルトでは、ヘルパーとコンポーネントのdata属性の指定は無視されます。
例えば、

```handlebars
{{#link-to "photos" data-toggle="dropdown"}}Photos{{/link-to}}

{{input type="text" data-toggle="tooltip" data-placement="bottom" title="Name"}}
```

<!--
renders the following HTML:
-->

と書いた場合、以下のHTMLが出力されます。

```html
<a id="ember239" class="ember-view" href="#/photos">Photos</a>

<input id="ember257" class="ember-view ember-text-field" type="text"
       title="Name">
```

<!--
To enable support for data attributes an attribute binding must be
added to the component, e.g.
[`Ember.LinkComponent`](https://www.emberjs.com/api/ember/2.16/classes/LinkComponent)
or [`Ember.TextField`](https://www.emberjs.com/api/ember/2.16/classes/TextField)
for the specific attribute:
-->

data属性のサポートを有効にするには、`attributeBindings`をコンポーネントで設定する必要があります。
上記の例では、[`Ember.LinkComponent`](https://www.emberjs.com/api/ember/2.16/classes/LinkComponent)と [`Ember.TextField`](https://www.emberjs.com/api/ember/2.16/classes/TextField)を`reopen`して設定します。

```javascript
Ember.LinkComponent.reopen({
  attributeBindings: ['data-toggle']
});

Ember.TextField.reopen({
  attributeBindings: ['data-toggle', 'data-placement']
});
```

<!--
Now the same template above renders the following HTML:
-->

すると、上記と同じテンプレートで、以下のHTMLが描画されます。

```html
<a id="ember240" class="ember-view" href="#/photos" data-toggle="dropdown">Photos</a>

<input id="ember259" class="ember-view ember-text-field"
       type="text" data-toggle="tooltip" data-placement="bottom" title="Name">
```
