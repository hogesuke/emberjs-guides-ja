<!--
Part of what makes components so useful is that they let you take complete control of a section of the DOM.
This allows for direct DOM manipulation, listening and responding to browser events, and using 3rd party JavaScript libraries in your Ember app.
-->

コンポーネントが非常に便利な理由の1つは、DOMのセクションを完全に制御できるということです。
これにより、直接的なDOM操作、ブラウザイベントへの応答とリッスン、EmberアプリケーションでのサードパーティJavaScriptライブラリの使用が可能になります。

<!--
As components are rendered, re-rendered and finally removed, Ember provides _lifecycle hooks_ that allow you to run code at specific times in a component's life.
-->

コンポーネントが描画されたり、再描画されたり、最終的に削除される時に、Emberはライフサイクルフックを呼び、コンポーネントが動いている間の特定の時間に任意のコードを実行することができます。

<!--
To get the most use out of a component, it is important to understand these lifecycle methods.
-->

コンポーネントを最大限に使用するには、ライフサイクルの使い方を理解することが重要です。

<!--
## Order of Lifecycle Hooks Called
-->

## 呼び出されるライフサイクルフックの順序

<!--
Listed below are the component lifecycle [hooks](../../getting-started/core-concepts/#toc_hooks) in order of execution according to render scenario.
-->

以下は、描画時のシナリオに従って実行されるコンポーネントのライフサイクル[フック](../../getting-started/core-concepts/#toc_フック)を示しています。

<!--
### On Initial Render
-->

## 初期描画時

1. `init`
2. [`didReceiveAttrs`](#toc_formatting-component-attributes-with-code-didreceiveattrs-code)
3. `willRender`
4. [`didInsertElement`](#toc_integrating-with-third-party-libraries-with-code-didinsertelement-code)
5. [`didRender`](#toc_making-updates-to-the-rendered-dom-with-code-didrender-code)

<!--
### On Re-Render
-->

## 再描画時

1. [`didUpdateAttrs`](#toc_resetting-presentation-state-on-attribute-change-with-code-didupdateattrs-code)
2. [`didReceiveAttrs`](#toc_formatting-component-attributes-with-code-didreceiveattrs-code)
3. `willUpdate`
4. `willRender`
5. `didUpdate`
6. [`didRender`](#toc_making-updates-to-the-rendered-dom-with-code-didrender-code)

<!--
### On Component Destroy
-->

## 削除時

1. [`willDestroyElement`](#toc_detaching-and-tearing-down-component-elements-with-code-willdestroyelement-code)
2. `willClearRender`
3. `didDestroyElement`

<!--
## Lifecycle Hook Examples
-->

## ライフサイクルフックの例

<!--
Below are some samples of ways to use lifecycle hooks within your components.
-->

以下に、コンポーネント内でライフサイクルフックを使用する方法のサンプルをいくつか示します。

<!--
### Resetting Presentation State on Attribute Change with `didUpdateAttrs`
-->

### `didUpdateAttrs`による属性変更に関するプレゼンテーション状態のリセット

<!--
`didUpdateAttrs` runs when the attributes of a component have changed, but not when the component is re-rendered, via `component.rerender`,
`component.set`, or changes in models or services used by the template.
-->

`didUpdateAttrs`は、コンポーネントの属性が変更されたときに実行されますが、コンポーネントが再描画される時や、`component.rerender`、`component.set`、またはテンプレートで使用しているモデルやサービスの変更では実行されません。

<!--
Since `didUpdateAttrs` is called prior to rerender, you can use this hook to execute code when specific attributes are changed.
This hook can be an effective alternative to an observer, as it will run prior to a re-render, but after an attribute has changed.
-->

`didUpdateAttrs`は再描画の前に呼び出されるので、特定の属性が変更されたときにこのフックを使用してコードを実行できます。
このフックは、再描画の前、属性が変更された後に実行されるため、オブザーバに代わる有効な代替手段となります。

<!--
An example of this scenario in action is a profile editor component.  As you are editing one user, and the user attribute is changed,
you can use `didUpdateAttrs` to clear any error state that was built up from editing the previous user.
-->

実例としてプロフィール編集コンポーネントを説明します。
1人のユーザーを編集中に、ユーザー属性が変更された場合、`didUpdateAttrs`を使用して、前のユーザーの編集で作成されたエラー状態をクリアできます。

```app/templates/components/profile-editor.hbs
<ul class="errors">
  {{#each errors as |error|}}
    <li>{{error.message}}</li>
  {{/each}}
</ul>
<fieldset>
  {{input name="user.name" value=name change=(action "required")}}
  {{input name="user.department" value=department change=(action "required")}}
  {{input name="user.email" value=email change=(action "required")}}
</fieldset>
```

```/app/components/profile-editor.js
import Component from '@ember/component';

export default Component.extend({
  init() {
    this._super(...arguments);
    this.errors = [];
  },

  didUpdateAttrs() {
    this._super(...arguments);
    this.set('errors', []);
  },

  actions: {
    required(event) {
      if (!event.target.value) {
        this.get('errors').pushObject({ message: `${event.target.name} is required`});
      }
    }
  }
});
```

<!--
### Formatting Component Attributes with `didReceiveAttrs`
-->

### `didReceiveAttrs`によるコンポーネント属性の書式設定

<!--
`didReceiveAttrs` runs after `init`, and it also runs on subsequent re-renders, which is useful for logic that is the same on all renders.
It does not run when the re-render has been initiated internally.
-->

`didReceiveAttrs`は`init`の後に実行され、その後の再描画の時も実行されます。
これは、すべての描画時で同じロジックを実行するのに便利です。
再描画が内部的に開始されたときには実行されません。

<!--
Since the `didReceiveAttrs` hook is called every time a component's attributes are updated whether on render or re-render,
you can use the hook to effectively act as an observer, ensuring code is executed every time an attribute changes.
-->

`didReceiveAttrs`フックは、描画時または再描画時にコンポーネントの属性が更新されるたびに呼び出されるため、このフックを使用してオブザーバとして効果的に機能させ、属性が変更されるたびにコードが確実に実行されるようできます。

<!--
For example, if you have a component that renders based on a json configuration, but you want to provide your component with the option of taking the config as a string,
you can leverage `didReceiveAttrs` to ensure the incoming config is always parsed.
-->

例えば、jsonに基づいて描画するコンポーネントがあり、そのコンポーネントに文字列でも設定を渡すオプションを渡したい場合に、`didReceiveAttrs`を利用して、受け取った設定を常にパーズするようにすることができます。

```app/components/profile-editor.js
import Component from '@ember/component';

export default Component.extend({
  didReceiveAttrs() {
    this._super(...arguments);
    const profile = this.get('data');
    if (typeof profile === 'string') {
      this.set('profile', JSON.parse(profile));
    } else {
      this.set('profile', profile);
    }
  }
});
```

<!--
### Integrating with Third-Party Libraries with `didInsertElement`
-->

### `didInsertElement`を使用した第三者のライブラリとの統合

<!--
Suppose you want to integrate your favorite date picker library into an Ember project.
Typically, 3rd party JS/jQuery libraries require a DOM element to bind to.
So, where is the best place to initialize and attach the library?
-->

お気に入りの日付選択ライブラリをEmberプロジェクトに統合したいとします。
通常、サードパーティのJS/jQueryライブラリでは、バインドするにはDOM要素が必要です。
では、ライブラリを初期化して添付するのに最適な場所はどこでしょうか？

<!--
After a component successfully renders its backing HTML element into the DOM, it will trigger its [`didInsertElement()`][did-insert-element] hook.
-->

コンポーネントのHTML要素がDOMに描画されると、[`didInsertElement()`][did-insert-element]フックが呼ばれます。

<!--
Ember guarantees that, by the time `didInsertElement()` is called:
-->

Emberは、`didInsertElement()`が呼び出されるまでに、次のことを保証します。

<!--
1. The component's element has been both created and inserted into the
   DOM.
2. The component's element is accessible via the component's
   [`$()`][dollar]
   method.
-->

1. コンポーネントの要素が作成され、DOMに挿入されていルコと
2. コンポーネントの要素がコンポーネントの[`$()`][dollar]メソッドを介してアクセスできること

<!--
A component's [`$()`][dollar] method allows you to access the component's DOM element by returning a JQuery element.
For example, you can set an attribute using jQuery's `attr()` method:
-->

コンポーネントの[`$()`][dollar]メソッドを使用すると、JQueryのオブジェクトが返され、それを使ってコンポーネントのDOM要素にアクセスできます。
例えば、jQueryの`attr()`メソッドを使用して属性を設定できます。

```app/components/profile-editor.js
import Component from '@ember/component';

export default Component.extend({
  didInsertElement() {
    this._super(...arguments);
    this.$().attr('contenteditable', true);
  }
});
```

<!--
[`$()`][dollar] will, by default, return a jQuery object for the component's root element, but you can also target child elements within the component's template by passing a selector:
-->

[`$()`][dollar]はデフォルトでコンポーネントのルート要素のjQueryオブジェクトを返しますが、セレクタを渡すことでコンポーネントのテンプレート内の子要素を対象にすることもできます。

```app/components/profile-editor.js
import Component from '@ember/component';

export default Component.extend({
  didInsertElement() {
    this._super(...arguments);
    this.$('div p button').addClass('enabled');
  }
});
```

<!--
Let's initialize our date picker by overriding the [`didInsertElement()`][did-insert-element] method.
-->

[`didInsertElement()`][did-insert-element]メソッドをオーバーライドして、日付ピッカーを初期化しましょう。

<!--
Date picker libraries usually attach to an `<input>` element, so we will use jQuery to find an appropriate input within our component's template.
-->

日付ピッカーライブラリは通常`<input>`要素にアタッチするので、jQueryを使用してコンポーネントのテンプレート内の適切なinput要素を探します。

```app/components/profile-editor.js
import Component from '@ember/component';

export default Component.extend({
  didInsertElement() {
    this._super(...arguments);
    this.$('input.date').myDatePickerLib();
  }
});
```

<!--
[`didInsertElement()`][did-insert-element] is also a good place to
attach event listeners. This is particularly useful for custom events or
other browser events which do not have a [built-in event
handler][event-names].
-->

[`didInsertElement()`][did-insert-element]は、イベントリスナーをアタッチするのにも適しています。
これは、カスタムイベントや[組み込みのイベントハンドラ][event-names]を持たない他のブラウザイベントで特に便利です。

<!--
For example, perhaps you have some custom CSS animations trigger when the component
is rendered and you want to handle some cleanup when it ends:
-->

例えば、コンポーネントが描画され時にカスタムCSSアニメーションを実行し、終了時にいくつかのクリーンアップを処理したい場合は以下のようにします。

```app/components/profile-editor.js
import Component from '@ember/component';

export default Component.extend({
  didInsertElement() {
    this._super(...arguments);
    this.$().on('animationend', () => {
      $(this).removeClass('sliding-anim');
    });
  }
});
```

<!--
There are a few things to note about the `didInsertElement()` hook:
-->

`didInsertElement()`フックについて注意すべき点がいくつかあります。

<!--
- It is only triggered once when the component element is first rendered.
- In cases where you have components nested inside other components, the child component will always receive the `didInsertElement()` call before its parent does.
- Setting properties on the component in [`didInsertElement()`][did-insert-element] triggers a re-render, and for performance reasons,
  is not allowed.
- While [`didInsertElement()`][did-insert-element] is technically an event that can be listened for using `on()`, it is encouraged to override the default method itself,
  particularly when order of execution is important.
-->

- このフックはコンポーネント要素の初期描画時に初めて呼び出されます
- 他のコンポーネントの中にコンポーネントがネストされている場合、子コンポーネントの`didInsertElement()`が親コンポーネントより常に先に呼び出されます
- [`didInsertElement()`][did-insert-element]でコンポーネントのプロパティを設定すると再描画が走るため、パフォーマンス上の理由から許可されていません
- [`didInsertElement()`][did-insert-element]は厳密には`on()`を使用してリッスンできるイベントですが、特に実行順序が重要な場合は、デフォルトのメソッド自体をオーバーライドすることが推奨されています


[did-insert-element]: https://www.emberjs.com/api/ember/release/classes/Component/events/didInsertElement?anchor=didInsertElement
[dollar]: https://www.emberjs.com/api/ember/release/classes/Component/methods/$?anchor=%24
[event-names]: http://guides.emberjs.com/v2.1.0/components/handling-events/#toc_event-names


<!--
### Making Updates to the Rendered DOM with `didRender`
-->

### `didRender`でDOMが描画された後に処理を実行する

<!--
The `didRender` hook is called during both render and re-render after the template has rendered and the DOM updated.
You can leverage this hook to perform post-processing on the DOM of a component after it's been updated.
-->

`didRender`フックは、テンプレートが描画され、DOMが更新された後、描画と再描画時の両方で呼び出されます。
このフックを利用して、コンポーネントのDOMが更新された後で処理を実行できます。

<!--
In this example, there is a list component that needs to scroll to a selected item when rendered.
Since scrolling to a specific spot is based on positions within the DOM, we need to ensure that the list has been rendered before scrolling.
We can first render this list, and then set the scroll.
-->

この例では、描画時に選択項目にスクロールする必要があるリストコンポーネントがあります。
特定の場所へのスクロールはDOM内の位置に基づくため、スクロールする前にリストが描画されていることを確認する必要があります。
リストを先に描画してからスクロールが可能になります。

<!--
The component below takes a list of items and displays them on the screen.
Additionally, it takes an object representing which item is selected and will select and set the scroll top to that item.
-->

以下のコンポーネントはアイテムのリストを取得し、それらを画面に表示します。
さらに、どの項目が選択されているかを表すオブジェクトを取り、それ選択してその項目にスクロールトップを設定します。

```app/templates/application.hbs
{{selected-item-list items=items selectedItem=selection}}
```

<!--
When rendered the component will iterate through the given list and apply a class to the one that is selected.
-->

描画されると、コンポーネントは指定されたリストを反復し、選択中のものにクラスを適用します。

```app/templates/components/selected-item-list.hbs
{{#each items as |item|}}
  <div class="list-item {{if item.isSelected 'selected-item'}}">{{item.label}}</div>
{{/each}}
```

<!--
The scroll happens on `didRender`, where it will scroll the component's container to the element with the selected class name.
-->

スクロールは`didRender`で行い、選択されたクラス名を持つ要素にコンポーネントのコンテナをスクロールさせます。

```/app/components/selected-item-list.js
import Component from '@ember/component';

export default Component.extend({
  classNames: ['item-list'],

  didReceiveAttrs() {
    this._super(...arguments);
    this.set('items', this.get('items').map((item) => {
      if (item.id === this.get('selectedItem.id')) {
        item.isSelected = true;
      }
      return item;
    }));
  },

  didRender() {
    this._super(...arguments);
    this.$('.item-list').scrollTop(this.$('.selected-item').position.top);
  }
});
```

<!--
### Detaching and Tearing Down Component Elements with `willDestroyElement`
-->

### `willDestroyElement`でコンポーネントのデタッチと削除

<!--
When a component detects that it is time to remove itself from the DOM, Ember will trigger the [`willDestroyElement()`](https://www.emberjs.com/api/ember/release/classes/Component/events/willDestroyElement?anchor=willDestroyElement) method,
allowing for any teardown logic to be performed.
-->

コンポーネントがDOMから自身を削除する時間であることを検出すると、Emberは[`willDestroyElement()`](https://www.emberjs.com/api/ember/release/classes/Component/events/willDestroyElement?anchor=willDestroyElement)メソッドを呼び出し、解体処理を実行できるようにします。

<!--
Component teardown can be triggered by a number of different conditions.
For instance, the user may navigate to a different route, or a conditional Handlebars block surrounding your component may change:
-->

コンポーネントの解体は、さまざまな条件によって呼び出されます。
例えば、ユーザーが別のルートに移動したり、コンポーネントを囲む条件分岐Handlebarsブロックの変更などです。


```app/templates/application.hbs
{{#if falseBool}}
  {{my-component}}
{{/if}}
```

<!--
Let's use this hook to cleanup our date picker and event listener from above:
-->

このフックを使用して、上記で設定した日付ピッカーのイベントリスナーをクリーンアップしましょう。

```app/components/profile-editor.js
import Component from '@ember/component';

export default Component.extend({
  willDestroyElement() {
    this.$().off('animationend');
    this.$('input.date').myDatepickerLib().destroy();
    this._super(...arguments);
  }
});
```
