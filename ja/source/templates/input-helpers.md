<!--
The [`{{input}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=input)
and [`{{textarea}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=textarea)
helpers in Ember.js are the easiest way to create common form controls.
Using these helpers, you can create form controls that are almost identical to the native HTML `<input>` or `<textarea>` elements, but are aware of Ember's two-way bindings and can automatically update.
-->

Ember.jsの[`{{input}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=input)と[`{{textarea}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=textarea)ヘルパーにより、簡単にフォームを作成することができます。
このヘルパーを使用すると、ネイティブのHTML`<input>`や`<textarea>`要素とほとんど同じフォームを作成でき、Emberの双方向バインディングによって自動で値を同期することができます。

## Text fields

```handlebars
{{input value="http://www.facebook.com"}}
```

<!--
Will become:
-->

上記の例は以下のようになります。

```html
<input type="text" value="http://www.facebook.com"/>
```

<!--
You can pass the following standard `<input>` attributes within the input
helper:
-->

inputヘルパーには、以下の`<input>`要素の標準の属性を渡すことができます。

<table>
  <tr><td>`readonly`</td><td>`required`</td><td>`autofocus`</td></tr>
  <tr><td>`value`</td><td>`placeholder`</td><td>`disabled`</td></tr>
  <tr><td>`size`</td><td>`tabindex`</td><td>`maxlength`</td></tr>
  <tr><td>`name`</td><td>`min`</td><td>`max`</td></tr>
  <tr><td>`pattern`</td><td>`accept`</td><td>`autocomplete`</td></tr>
  <tr><td>`autosave`</td><td>`formaction`</td><td>`formenctype`</td></tr>
  <tr><td>`formmethod`</td><td>`formnovalidate`</td><td>`formtarget`</td></tr>
  <tr><td>`height`</td><td>`inputmode`</td><td>`multiple`</td></tr>
  <tr><td>`step`</td><td>`width`</td><td>`form`</td></tr>
  <tr><td>`selectionDirection`</td><td>`spellcheck`</td><td>`type`</td></tr>
</table>

<!--
If these attributes are set to a quoted string, their values will be set
directly on the element, as in the previous example. However, when left
unquoted, these values will be bound to a property on the template's current
rendering context. For example:
-->

これらの属性が引用符(")で囲まれた文字列に設定されている場合、その値は上記の例のように要素に直接設定されます。
引用符を付けない場合、値はテンプレートのコンテキスト(コントローラやコンポーネント)のプロパティにバインドされます。

```handlebars
{{input type="text" value=firstName disabled=entryNotAllowed size="50"}}
```

<!--
Will bind the `disabled` attribute to the value of `entryNotAllowed` in the
current context.
-->

上記の例では、`disabled`属性はコンテキストの`entryNotAllowed`の値にバインドされます。

<!--
## Actions
-->

## イベントの指定

<!--
To dispatch an action on specific events, such as `enter` or `key-press`, use the following
-->

`enter`や`key-press`といったイベントを指定する場合は以下のようにします。


```handlebars
{{input value=firstName key-press="updateFirstName"}}
```

<!--
[Event Names](https://www.emberjs.com/api/ember/2.16/classes/Component#toc_event-names) must be dasherized.
-->

[イベント名](https://www.emberjs.com/api/ember/2.16/classes/Component#toc_event-names)はケバブケース(ダッシュで単語間を区切る)でないといけません。

<!--
## Checkboxes
-->

## チェックボックス

<!--
You can also use the
[`{{input}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=input)
helper to create a checkbox by setting its `type`:
-->

チェックボックスを作成するには、[`{{input}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/if?anchor=input)の`type`を指定します。

```handlebars
{{input type="checkbox" name="isAdmin" checked=isAdmin}}
```

<!--
Checkboxes support the following properties:
-->

チェックボックスでは以下の属性がサポートされています。


* `checked`
* `disabled`
* `tabindex`
* `indeterminate`
* `name`
* `autofocus`
* `form`

<!--
Which can be bound or set as described in the previous section.
-->

前のセクションで説明した通り、バインドか設定ができます。

<!--
## Text Areas
-->

## テキストエリア

```handlebars
{{textarea value=name cols="80" rows="6"}}
```

<!--
Will bind the value of the text area to `name` on the current context.
-->

上記の例では、コンテキストの`name`とでテキストエリアの値をバインドしています。

<!--
[`{{textarea}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/textarea?anchor=textarea) supports binding and/or setting the following properties:
-->

[`{{textarea}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/textarea?anchor=textarea)では、以下の属性のバインドと設定をサポートしています。

* `value`
* `name`
* `rows`
* `cols`
* `placeholder`
* `disabled`
* `maxlength`
* `tabindex`
* `selectionEnd`
* `selectionStart`
* `selectionDirection`
* `wrap`
* `readonly`
* `autofocus`
* `form`
* `spellcheck`
* `required`

<!--
### Binding dynamic attribute
-->

### 動的属性のバインディング

<!--
You might need to bind a property dynamically to an input if you're building a flexible form, for example. To achieve this you need to use the [`{{get}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/get?anchor=get) and [`{{mut}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/mut?anchor=mut) in conjunction like shown in the following example:
-->

柔軟なフォームを作成する際、プロパティをinput要素に動的にバインドする必要がある場合があります。
これを実現するには、次の例のように [`{{get}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/get?anchor=get)と[`{{mut}}`](https://www.emberjs.com/api/ember/2.16/classes/Ember.Templates.helpers/methods/mut?anchor=mut)を併用します。

```handlebars
{{input value=(mut (get person field))}}
```

<!--
The `{{get}}` helper allows you to dynamically specify which property to bind, while the `{{mut}}` helper allows the binding to be updated from the input. See the respective helper documentation for more detail.
-->

`{{get}}`ヘルパーではバインドするプロパティを動的に指定しています。
`{{mut}}`ヘルパーではバインドしたものを更新できるようにしています。 
詳細については、各ヘルパーのドキュメントを参照してください。