<!--
By default, each component is backed by a `<div>` element. If you were
to look at a rendered component in your developer tools, you would see
a DOM representation that looked something like:
-->

デフォルトでは各コンポーネントは`<div>`要素に入ります。
描画されたコンポーネントをデベロッパーツールで確認すると、以下のようなDOMになっているでしょう。

```html
<div id="ember180" class="ember-view">
  <h1>My Component</h1>
</div>
```

<!--
You can customize what type of element Ember generates for your
component, including its attributes and class names, by creating a
subclass of `Component` in your JavaScript.
-->

Emberでコンポーネントの要素や属性やクラス名などをカスタマイズするには、JavaScriptで`Component`のサブクラスを作成して指定します。

<!--
### Customizing the Element
-->

### 要素のカスタマイズ

<!--
To use a tag other than `div`, subclass `Component` and assign it
a `tagName` property. This property can be any valid HTML5 tag name as a
string.
-->

`div`以外のタグを使用するには、`Component`をサブクラス化し、`tagName`プロパティを指定します。
このプロパティでは、HTML5タグを文字列として渡します。

```app/components/navigation-bar.js
import Component from '@ember/component';

export default Component.extend({
  tagName: 'nav'
});
```

```app/templates/components/navigation-bar.hbs
<ul>
  <li>{{#link-to "home"}}Home{{/link-to}}</li>
  <li>{{#link-to "about"}}About{{/link-to}}</li>
</ul>
```

<!--
### Customizing the Element's Class
-->

### 要素のクラスのカスタマイズ

<!--
You can specify the class of a component's element at invocation time the same
way you would for a regular HTML element:
-->

通常のHTML要素と同様に、呼び出し時にコンポーネントのクラスを指定できます。

```hbs
{{navigation-bar class="primary"}}
```

<!--
You can also specify which class names are applied to the component's
element by setting its `classNames` property to an array of strings:
-->

`classNames`プロパティを文字列の配列で設定することで、コンポーネントの要素に適用されるクラス名を指定することもできます。

```app/components/navigation-bar.js
import Component from '@ember/component';

export default Component.extend({
  classNames: ['primary']
});
```

<!--
If you want class names to be determined by properties of the component,
you can use class name bindings. If you bind to a Boolean property, the
class name will be added or removed depending on the value:
-->

コンポーネントのプロパティによってクラス名を決定する場合は、`classNameBindings`を指定します。
ブール値のプロパティにバインドすると、値に応じてクラス名が追加または削除されます。

```app/components/todo-item.js
import Component from '@ember/component';

export default Component.extend({
  classNameBindings: ['isUrgent'],
  isUrgent: true
});
```

<!--
This component would render the following:
-->

このコンポーネントは以下のように描画されます。

```html
<div class="ember-view is-urgent"></div>
```

<!--
If `isUrgent` is changed to `false`, then the `is-urgent` class name will be removed.
-->

`isUrgent`が`false`に変更された場合、`is-urgent`クラス名は削除されます。

<!--
By default, the name of the Boolean property is dasherized. You can customize the class name
applied by delimiting it with a colon:
-->

クラス名はデフォルトでブール値のプロパティの名前をスネークケースに変換したものが使われます。
クラス名を指定するには、コロンで区切って後ろにクラス名を書きます。

```app/components/todo-item.js
import Component from '@ember/component';

export default Component.extend({
  classNameBindings: ['isUrgent:urgent'],
  isUrgent: true
});
```

<!--
This would render this HTML:
-->

上記の例は以下のように描画されます。

```html
<div class="ember-view urgent">
```

<!--
Besides the custom class name for the value being `true`, you can also specify a class name which is used when the value is `false`:
-->

値が`true`の時のクラス名に加えて、値が`false`の時に使用されるクラス名を指定することもできます。

```app/components/todo-item.js
import Component from '@ember/component';

export default Component.extend({
  classNameBindings: ['isEnabled:enabled:disabled'],
  isEnabled: false
});
```

<!--
This would render this HTML:
-->

上記の例は以下のように描画されます。

```html
<div class="ember-view disabled">
```

<!--
You can also specify a class which should only be added when the property is
`false` by declaring `classNameBindings` like this:
-->

`classNameBindings`を次のように宣言することで、プロパティが`false`の場合にのみ追加するクラスを指定することもできます。

```app/components/todo-item.js
import Component from '@ember/component';

export default Component.extend({
  classNameBindings: ['isEnabled::disabled'],
  isEnabled: false
});
```

<!--
This would render this HTML:
-->

上記の例は以下のように描画されます。

```html
<div class="ember-view disabled">
```

<!--
If the `isEnabled` property is set to `true`, no class name is added:
-->

`isEnabled`プロパティが`true`の場合はクラス名は追加されません。

```html
<div class="ember-view">
```

<!--
If the bound property's value is a string, that value will be added as a class name without
modification:
-->

バインドされたプロパティの値が文字列の場合、その値は変更されずにクラス名として追加されます。

```app/components/todo-item.js
import Component from '@ember/component';

export default Component.extend({
  classNameBindings: ['priority'],
  priority: 'highestPriority'
});
```

<!--
This would render this HTML:
-->

上記の例は以下のように描画されます。

```html
<div class="ember-view highestPriority">
```

<!--
### Customizing Attributes
-->

### 属性のカスタマイズ

<!--
You can bind attributes to the DOM element that represents a component
by using `attributeBindings`:
-->

`attributeBindings`を使用して、コンポーネントのプロパティとDOM要素の属性をバインドできます。

```app/components/link-item.js
import Component from '@ember/component';

export default Component.extend({
  tagName: 'a',
  attributeBindings: ['href'],

  href: 'http://emberjs.com'
});
```

<!--
You can also bind these attributes to differently named properties:
-->

属性を別の名前のプロパティにバインドすることもできます。

```app/components/link-item.js
import Component from '@ember/component';

export default Component.extend({
  tagName: 'a',
  attributeBindings: ['customHref:href'],

  customHref: 'http://emberjs.com'
});
```

<!--
If the attribute is null, it won't be rendered:
-->

属性がnullの場合は描画されません。

```app/components/link-item.js
import Component from '@ember/component';

export default Component.extend({
  tagName: 'span',
  attributeBindings: ['title'],

  title: null,
});
```

<!--
This would render this HTML when no title is passed to the component:
-->

コンポーネントにtitleが渡されていない時は、以下のように描画されます。

```html
<span class="ember-view">
```

<!--
...and this HTML when a title of "Ember JS" is passed to the component:
-->

titleに"Ember JS"が指定されている時は、以下のように描画されます。

```html
<span class="ember-view" title="Ember JS">
```
