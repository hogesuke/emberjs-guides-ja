<!--
*__Note:__ Observers are often over-used by new Ember developers. Observers are used
heavily within the Ember framework itself, but for most problems Ember app
developers face, computed properties are the appropriate solution.*
-->

*__注記__:Emberを始めたばかりの開発者はオブザーバーを乱用しがちです。
オブザーバーはEmberフレームワーク自体の中ではよく使用されていますが、Emberアプリケーション開発者が直面する問題のほとんどが、算出プロパティで適切に解決できます。*

<!--
Ember supports observing any property, including computed properties.
-->

Emberは、算出プロパティを含むすべてのプロパティの監視(observing)をサポートしています。

<!--
Observers should contain behavior that reacts to changes in another property.
Observers are especially useful when you need to perform some behavior after a
binding has finished synchronizing.
-->

オブザーバーには、別のプロパティの変更に反応する動作が含まれている必要があります。
オブザーバーは、バインディングの同期が完了した後に、何らかの動作を実行する必要がある場合に特に便利です。

<!--
You can set up an observer on an object by using `observer`:
-->

`observer`を使用してオブジェクト上にオブザーバーを設定できます。

```javascript
import EmberObject, {
  computed,
  observer
} from '@ember/object';

Person = EmberObject.extend({
  // these will be supplied by `create`
  firstName: null,
  lastName: null,

  fullName: computed('firstName', 'lastName', function() {
    return `${this.get('firstName')} ${this.get('lastName')}`;
  }),

  fullNameChanged: observer('fullName', function() {
    // deal with the change
    console.log(`fullName changed to: ${this.get('fullName')}`);
  })
});

let person = Person.create({
  firstName: 'Yehuda',
  lastName: 'Katz'
});

// observer won't fire until `fullName` is consumed first
person.get('fullName'); // "Yehuda Katz"
person.set('firstName', 'Brohuda'); // fullName changed to: Brohuda Katz
```

<!--
Because the `fullName` computed property depends on `firstName`,
updating `firstName` will fire observers on `fullName` as well.
-->

`fullName`の算出プロパティは`firstName`に依存するため、`firstName`を更新すると`fullName`のオブザーバーが作動します。

<!--
### Observers and asynchrony
-->

### オブザーバーと非同期処理

<!--
Observers in Ember are currently synchronous. This means that they will fire
as soon as one of the properties they observe changes. Because of this, it
is easy to introduce bugs where properties are not yet synchronized:
-->

Emberのオブザーバーはすぐに同期します。
これは、監視しているプロパティが1つでも変更されると直ちに作動することを意味します。
そのため、他のプロパティが同期が完了していないところは、バグが混ざりやすいでしょう。

<!--
```javascript
import { observer } from '@ember/object';

Person.reopen({
  lastNameChanged: observer('lastName', function() {
    // The observer depends on lastName and so does fullName. Because observers
    // are synchronous, when this function is called the value of fullName is
    // not updated yet so this will log the old value of fullName
    console.log(this.get('fullName'));
  })
});
```
-->

```javascript
import { observer } from '@ember/object';

Person.reopen({
  lastNameChanged: observer('lastName', function() {
    // オブザーバーとfullNameはlastNameに依存しています。
    // オブザーバーはすぐに同期するので、この関数が呼び出される時のfullNameの値は
    // まだ更新されていないので、fullNameの古い値がログに記録されます。
    console.log(this.get('fullName'));
  })
});
```

<!--
This synchronous behavior can also lead to observers being fired multiple
times when observing multiple properties:
-->

この同期の仕様は、複数のプロパティを監視しているときにオブザーバーが複数回作動する原因ともなります。

<!--
```javascript
import { observer } from '@ember/object';

Person.reopen({
  partOfNameChanged: observer('firstName', 'lastName', function() {
    // Because both firstName and lastName were set, this observer will fire twice.
  })
});

person.set('firstName', 'John');
person.set('lastName', 'Smith');
```
-->

```javascript
import { observer } from '@ember/object';

Person.reopen({
  partOfNameChanged: observer('firstName', 'lastName', function() {
    // firstNameとlastNameの両方が設定されているため、このオブザーバは2回作動します。
  })
});

person.set('firstName', 'John');
person.set('lastName', 'Smith');
```

<!--
To get around these problems, you should make use of [`Ember.run.once()`](https://www.emberjs.com/api/ember/release/classes/@ember%2Frunloop/methods/once?anchor=once).
This will ensure that any processing you need to do only happens once, and
happens in the next run loop once all bindings are synchronized:
-->

これらの問題を回避するには、[`Ember.run.once()`](https://www.emberjs.com/api/ember/release/classes/@ember%2Frunloop/methods/once?anchor=once)を使用する必要があります。
これにより、必要な処理は一度だけ実行され、すべてのバインディングが同期されると次の実行ループで処理が行われます。


<!--
```javascript
import { observer } from '@ember/object';
import { once } from "@ember/runloop"

Person.reopen({
  partOfNameChanged: observer('firstName', 'lastName', function() {
    once(this, 'processFullName');
  }),

  processFullName() {
    // This will only fire once if you set two properties at the same time, and
    // will also happen in the next run loop once all properties are synchronized
    console.log(this.get('fullName'));
  }
});

person.set('firstName', 'John');
person.set('lastName', 'Smith');
```
-->

```javascript
import { observer } from '@ember/object';
import { once } from "@ember/runloop"

Person.reopen({
  partOfNameChanged: observer('firstName', 'lastName', function() {
    once(this, 'processFullName');
  }),

  processFullName() {
    // 同時に2つのプロパティを設定しても、1回だけ実行されます。
    // すべてのプロパティの同期完了後の次の実行ループでも実行されます。
    console.log(this.get('fullName'));
  }
});

person.set('firstName', 'John');
person.set('lastName', 'Smith');
```

<!--
### Observers and object initialization
-->

### オブザーバとオブジェクトの初期化

<!--
Observers never fire until after the initialization of an object is complete.
-->

オブザーバーは、オブジェクトの初期化が完了するまで実行されません。

<!--
If you need an observer to fire as part of the initialization process, you
cannot rely on the side effect of `set`. Instead, specify that the observer
should also run after `init` by using [`Ember.on()`](https://emberjs.com/api/ember/2.15/namespaces/Ember/methods/on?anchor=on):
-->

初期化プロセスの一環としてオブザーバーを作動させる必要がある場合も、`set`の副作用に頼るべきではありません。
代わりに、[`Ember.on()`](https://emberjs.com/api/ember/2.15/namespaces/Ember/methods/on?anchor=on)を使かってオブザーバーを`init`の後に実行するように指定します。

```javascript
import EmberObject, { observer } from '@ember/object';
import { on } from '@ember/object/evented';

Person = EmberObject.extend({
  init() {
    this.set('salutation', 'Mr/Ms');
  },

  salutationDidChange: on('init', observer('salutation', function() {
    // some side effect of salutation changing
  }))
});
```

<!--
### Unconsumed Computed Properties Do Not Trigger Observers
-->

### 未使用の算出プロパティはオブザーバーを起動しない

<!--
If you never `get()` a computed property, its observers will not fire even if
its dependent keys change. You can think of the value changing from one unknown
value to another.
-->

オブザーバーが監視している算出プロパティを一度も`get()`しない場合、オブザーバーの依存キーが変更されても、オブザーバーは動作しません。
値が未知の値から別の値に変化すると考えることができます。

<!--
This doesn't usually affect application code because computed properties are
almost always observed at the same time as they are fetched. For example, you get
the value of a computed property, put it in DOM (or draw it with D3), and then
observe it so you can update the DOM once the property changes.
-->

これは、通常、算出プロパティが取得されるのとほぼ同時に監視されるため、アプリケーションコードには影響しません。
たとえば、算出プロパティの値を取得してDOMに入れて(またはD3で描画する)、observeすると、プロパティが変更されたらDOMを更新することができます。

<!--
If you need to observe a computed property but aren't currently retrieving it,
get it in your `init()` method.
-->

算出プロパティを監視する必要があるがその時点では取得していない場合は、`init()`メソッドで取得します。

<!--
### Outside of class definitions
-->

### クラス定義の外

<!--
You can also add observers to an object outside of a class definition
using [`addObserver()`](https://www.emberjs.com/api/ember/release/classes/@ember%2Fobject%2Fobservers/methods/addObserver?anchor=addObserver):
-->

[`addObserver()`](https://www.emberjs.com/api/ember/release/classes/@ember%2Fobject%2Fobservers/methods/addObserver?anchor=addObserver)を使用して、オブザーバーをクラス定義外のオブジェクトに追加することもできます。


```javascript
person.addObserver('fullName', function() {
  // deal with the change
});
```
