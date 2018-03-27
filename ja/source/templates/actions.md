<!--
Your app will often need a way to let users interact with controls that
change application state. For example, imagine that you have a template
that shows a blog title, and supports expanding the post to show the body.
-->

アプリでは、ユーザーの操作によってアプリケーションの状態を変更できるようにすることがよくあります。
例えば、ブログのタイトルを表示するテンプレートがあり、記事の本文を「さらに読む」などで広げて表示できるようにするとします。

<!--
If you add the
[`{{action}}`](https://www.emberjs.com/api/ember/release/classes/Ember.Templates.helpers/methods/action?anchor=action)
helper to any HTML DOM element, when a user clicks the element, the named event
will be sent to the template's corresponding component or controller.
-->

HTML DOM要素に[`{{action}}`](https://www.emberjs.com/api/ember/release/classes/Ember.Templates.helpers/methods/action?anchor=action)ヘルパーを追加すると、ユーザーがその要素をクリックすると、指定されたイベントがテンプレートに付随するコンポーネントかコントローラに送信されます。

```app/templates/components/single-post.hbs
<h3><button {{action "toggleBody"}}>{{title}}</button></h3>
{{#if isShowingBody}}
  <p>{{{body}}}</p>
{{/if}}
```

<!--
In the component or controller, you can then define what the action does within
the `actions` hook:
-->

コンポーネントかコントローラの`actions`フック内にアクションの動作を定義します。

```app/components/single-post.js
import Component from '@ember/component';

export default Component.extend({
  actions: {
    toggleBody() {
      this.toggleProperty('isShowingBody');
    }
  }
});
```

<!--
You will learn about more advanced usages in the Component's [Triggering Changes With Actions](../../components/triggering-changes-with-actions/) guide,
but you should familiarize yourself with the following basics first.
-->

コンポーネントのより高度な使い方については、[変更をアクションでトリガーする](../../components/triggering-changes-with-actions/)のところで説明していますが、その前に基本事項を熟知しておく必要があります。


<!--
## Action Parameters
-->

## アクションの引数

<!--
You can optionally pass arguments to the action handler. Any values
passed to the `{{action}}` helper after the action name will be passed to
the handler as arguments.
-->

アクションハンドラには引数を渡すことができます。
`{{action}}`ヘルパーのアクション名の後に渡された値はすべて引数としてハンドラに渡されます。

<!--
For example, if the `post` argument was passed:
-->

例えば、`post`を引数として渡すには、以下のようにします。


```handlebars
<p><button {{action "select" post}}>✓</button> {{post.title}}</p>
```

<!--
The `select` action handler would be called with a single argument
containing the post model:
-->

そうすると、`select`アクションハンドラは、postモデルを第1引数に取って呼び出されます。


```app/components/single-post.js
import Component from '@ember/component';

export default Component.extend({
  actions: {
    select(post) {
      console.log(post.get('title'));
    }
  }
});
```

<!--
## Specifying the Type of Event
-->

## イベントタイプの指定

<!--
By default, the
[`{{action}}`](https://www.emberjs.com/api/ember/release/classes/Ember.Templates.helpers/methods/action?anchor=action)
helper listens for click events and triggers the action when the user clicks
on the element.
-->

デフォルトでは、[`{{action}}`](https://www.emberjs.com/api/ember/release/classes/Ember.Templates.helpers/methods/action?anchor=action)ヘルパーはクリックイベントのため、ユーザーが要素をクリックしたときにアクションを呼び出します。

<!--
You can specify an alternative event by using the `on` option.
-->

`on`オプションを使用すると、別のイベントにできます。

```handlebars
<p>
  <button {{action "select" post on="mouseUp"}}>✓</button>
  {{post.title}}
</p>
```

<!--
You should use the <code>camelCased</code> event names, so two-word names like `keypress`
become `keyPress`.
-->

イベント名はキャメルケースで指定します。
`keypress`のような2単語の場合は`keyPress`のようになります。


<!--
## Allowing Modifier Keys
-->

## 修飾キーの許可

*訳注: 修飾キー(modifier keys)とは、シフト、コントロール、オルタネート(alt)キーなどのことです。*

<!--
By default, the `{{action}}` helper will ignore click events with
pressed modifier keys. You can supply an `allowedKeys` option
to specify which keys should not be ignored.
-->

デフォルトでは、`{{action}}`ヘルパーは修飾キーを押した状態のクリックイベントを無視します。
`allowedKeys`オプションを指定すると、無視しないキーを指定できます。

```handlebars
<button {{action "anActionName" allowedKeys="alt"}}>
  click me
</button>
```

<!--
This way the `{{action}}` will fire when clicking with the alt key
pressed down.
-->

こうするとaltキーを押した状態のクリックで`{{action}}`が呼ばれるようになります。

<!--
## Allowing Default Browser Action
-->

## ブラウザのデフォルトのアクションを許可する

<!--
By default, the `{{action}}` helper prevents the default browser action of the
DOM event. If you want to allow the browser action, you can stop Ember from
preventing it.
-->

デフォルトでは、`{{action}}`ヘルパーはDOMイベントのブラウザのデフォルトのアクションを防ぐため実行されません。
ブラウザーのデフォルトのアクションを実行したい場合は、Emberに防がせないよう指定します。

<!--
For example, if you have a normal link tag and want the link to bring the user
to another page in addition to triggering an ember action when clicked, you can
use `preventDefault=false`:
-->

例えば、通常のリンクタグがあり、リンクをクリックしてEmberのアクションを実行しつつ別のページに遷移させたい時は、`preventDefault=false`を指定します。


```handlebars
<a href="newPage.htm" {{action "logClick" preventDefault=false}}>Go</a>
```

<!--
With `preventDefault=false` omitted, if the user clicked on the link, Ember.js
will trigger the action, but the user will remain on the current page.
-->

`preventDefault=false`を省略すると、ユーザーがリンクをクリックした際に、Ember.jsがアクションを実行しますが、ユーザーはページに留まったままになります。

<!--
With `preventDefault=false` present, if the user clicked on the link, Ember.js
will trigger the action *and* the user will be directed to the new page.
-->

`preventDefault=false`が指定されている場合は、ユーザーがリンクをクリックすると、Ember.jsがアクションを実行し、ユーザーはリンク先のページに遷移します。

<!--
## Modifying the action's first parameter
-->

## アクションにeventのプロパティを渡す方法

<!--
If a `value` option for the
[`{{action}}`](https://www.emberjs.com/api/ember/release/classes/Ember.Templates.helpers/methods/action?anchor=action)
helper is specified, its value will be considered a property path that will
be read off of the first parameter of the action. This comes very handy with
event listeners and enables to work with one-way bindings.
-->

[`{{action}}`](https://www.emberjs.com/api/ember/release/classes/Ember.Templates.helpers/methods/action?anchor=action)ヘルパーで`value`オプションが指定されている場合、その値は、アクションの第1引数から読み取るプロパティ名(パス)と見なされます。
これはイベントリスナーで、単方向バインディングで処理する時にとてもに便利です。


```handlebars
<label>What's your favorite band?</label>
<input type="text" value={{favoriteBand}} onblur={{action "bandDidChange"}} />
```

<!--
Let's assume we have an action handler that prints its first parameter:
-->

第1引数を出力するアクションハンドラがあるとしましょう。

```js
actions: {
  bandDidChange(newValue) {
    console.log(newValue);
  }
}
```

<!--
By default, the action handler receives the first parameter of the event
listener, the event object the browser passes to the handler, so
`bandDidChange` prints `Event {}`.
-->

デフォルトでは、ブラウザがイベントリスナーの第1引数に渡すeventオブジェクトがアクションハンドラに渡されるので、`bandDidChange`は`Event {}`を出力します。

<!--
Using the `value` option modifies that behavior by extracting that property from
the event object:
-->

`value`オプションを使用すると、イベントオブジェクトから指定したプロパティを取得して渡すように変更できます。


```handlebars
<label>What's your favorite band?</label>
<input type="text" value={{favoriteBand}} onblur={{action "bandDidChange" value="target.value"}} />
```

<!--
The `newValue` parameter thus becomes the `target.value` property of the event
object, which is the value of the input field the user typed. (e.g 'Foo Fighters')
-->

したがって、`newValue`パラメーターは、イベントオブジェクトの`target.value`プロパティになります。
この場合、ユーザーが入力した入力フィールドの値になります。 (例: "Foo Fighters")

<!--
## Attaching Actions to Non-Clickable Elements
-->

## クリックできない要素での注意点

<!--
Note that actions may be attached to any element of the DOM, but not all
respond to the `click` event. For example, if an action is attached to an `a`
link without an `href` attribute, or to a `div`, some browsers won't execute
the associated function. If it's really needed to define actions over such
elements, a CSS workaround exists to make them clickable, `cursor: pointer`.
For example:
-->

アクションはDOMの任意の要素にアタッチすることができますが、すべての要素がクリックイベントに反応するわけではありません。
例えば、アクションが`href`属性を持っていない`a`要素や`div`にアタッチされている場合、ブラウザによっては関連する機能が動作しないことがあります。
そのような要素に対してアクションを定義することが本当に必要な場合のために、その要素がクリックできることを表現する`cursor: pointer`というCSSでの回避策があります。

例:

```css
[data-ember-action]:not(:disabled) {
  cursor: pointer;
}
```

<!--
Keep in mind that even with this workaround in place, the `click` event will
not automatically trigger via keyboard driven `click` equivalents (such as
the `enter` key when focused). Browsers will trigger this on clickable
elements only by default. This also doesn't make an element accessible to
users of assistive technology. You will need to add additional things like
`role` and/or `tabindex` to make this accessible for your users.
-->

この回避策を講じても、クリックイベントはキーボードの操作によるクリック(要素にフォーカスを当ててエンターキーを打つなど)には反応しないため注意が必要です。
ブラウザは、デフォルトでクリック可能な要素でのみ反応します。
この回避策は支援技術を使っているユーザーがその要素にアクセスできるようにもしてくれません。
ユーザーがアクセスできるようにするには、`role`や`tabindex`などを追加する必要があります。
