<!--
As you learn about Ember, you'll see code like `Component.extend()` and
`DS.Model.extend()`. Here, you'll learn about this `extend()` method, as well
as other major features of the Ember object model.
-->

Emberについて学ぶ際、`Component.extend()`や`DS.Model.extend()`などのコードを目にするでしょう。
ここでは、この`extend()`メソッド、およびEmberオブジェクトモデルのその他の主な機能について学美ます。

<!--
### Defining Classes
-->

### クラスの定義

<!--
To define a new Ember _class_, call the [`extend()`][1] method on
[`EmberObject`][2]:
-->

新しいEmberクラスを定義するには、[`EmberObject`][2]の[`extend()`][1]メソッドを呼び出します。

[1]: https://www.emberjs.com/api/ember/2.16/classes/@ember%2Fobject/methods/extend?anchor=extend
[2]: https://www.emberjs.com/api/ember/2.16/modules/@ember%2Fobject

```javascript
import EmberObject from '@ember/object';

const Person = EmberObject.extend({
  say(thing) {
    alert(thing);
  }
});
```

<!--
This defines a new `Person` class with a `say()` method.
-->

上記のコードは、`say()`メソッドを持つ`Person`クラスを定義しています。

<!--
You can also create a _subclass_ from any existing class by calling
its `extend()` method. For example, you might want to create a subclass
of Ember's built-in [`Component`][3] class:
-->

既存のクラスからサブクラスを作成するには、`extend()`メソッドを呼び出します。 たとえば、Emberのビルトインコンポーネントクラスのサブクラスを作成する場合、以下のようになります。

[3]: https://www.emberjs.com/api/ember/2.16/classes/Component

```app/components/todo-item.js
import Component from '@ember/component';

export default Component.extend({
  classNameBindings: ['isUrgent'],
  isUrgent: true
});
```

<!--
### Overriding Parent Class Methods
-->

### 親クラスのメソッドをオーバーライドする

<!--
When defining a subclass, you can override methods but still access the
implementation of your parent class by calling the special `_super()`
method:
-->

サブクラスを定義する場合、メソッドをオーバーライドできますが、
`_super()`を呼び出すことによって親クラスの同メソッドを実行できます。

<!--
```javascript
import EmberObject from '@ember/object';

const Person = EmberObject.extend({
  say(thing) {
    alert(`${this.get('name')} says: ${thing}`);
  }
});

const Soldier = Person.extend({
  say(thing) {
    // this will call the method in the parent class (Person#say), appending
    // the string ', sir!' to the variable `thing` passed in
    this._super(`${thing}, sir!`);
  }
});

let yehuda = Soldier.create({
  name: 'Yehuda Katz'
});

yehuda.say('Yes'); // alerts "Yehuda Katz says: Yes, sir!"
```
-->

```javascript
import EmberObject from '@ember/object';

const Person = EmberObject.extend({
  say(thing) {
    alert(`${this.get('name')}: ${thing}`);
  }
});

const Soldier = Person.extend({
  say(thing) {
    // 以下は親クラスのメソッド(Person#say)を実行します。
    // 引数の`thing`に「、了解しました」を付け足しています。
    this._super(`${thing}、 了解しました!`);
  }
});

let yehuda = Soldier.create({
  name: '矢吹田猫'
});

yehuda.say('はい'); // "矢吹田猫: はい、了解しした！"をアラート表示
```

<!--
In certain cases, you will want to pass arguments to `_super()` before or after overriding.
-->

場合によっては、オーバーライドの前または後に `_super()`に引数を渡したいと思うでしょう。

<!--
This allows the original method to continue operating as it normally would.
-->

これにより、親クラスの実装を通常通りに作動させることができます。

<!--
One common example is when overriding the [`normalizeResponse()`][4] hook in one of Ember-Data's serializers.
-->

 代表的な例に、Ember-Dataのシリアライザの[`normalizeResponse()`][4]フックをオーバーライドがあります。

<!--
A handy shortcut for this is to use a "spread operator", like `...arguments`:
-->

`...arguments`のように、スプレッド演算子を使うと便利です:

[4]: https://www.emberjs.com/api/ember-data/2.16/classes/DS.JSONAPISerializer/methods/normalizeResponse?anchor=normalizeResponse

<!--
```javascript
normalizeResponse(store, primaryModelClass, payload, id, requestType)  {
  // Customize my JSON payload for Ember-Data
  return this._super(...arguments);
}
```
-->

```javascript
normalizeResponse(store, primaryModelClass, payload, id, requestType)  {
  // Ember-Data様にJSONペイロードをカスタマイズ
  return this._super(...arguments);
}
```

<!--
The above example returns the original arguments (after your customizations) back to the parent class, so it can continue with its normal operations.
-->

上記の例では、カスタマイズ前の元の引数に戻して、それを親クラスに渡して、親クラスの通常の処理を実行しています。

<!--
### Creating Instances
-->

### インスタンスの作成

<!--
Once you have defined a class, you can create new _instances_ of that
class by calling its [`create()`][5] method. Any methods, properties and
computed properties you defined on the class will be available to
instances:
-->

クラスを定義したら、[`create()`][5]メソッドを呼び出すことでそのクラスの新しいインスタンスを作成できます。
クラスで定義したメソッド、プロパティ、および算出プロパティは、インスタンスで使用できるようになります。

[5]: https://www.emberjs.com/api/ember/2.16/classes/@ember%2Fobject/methods/create?anchor=create

```javascript
import EmberObject from '@ember/object';

const Person = EmberObject.extend({
  say(thing) {
    alert(`${this.get('name')} says: ${thing}`);
  }
});

let person = Person.create();

person.say('Hello'); // alerts " says: Hello"
```

<!--
When creating an instance, you can initialize the values of its properties
by passing an optional hash to the `create()` method:
-->

インスタンスを作成する際、プロパティを初期値を指定できます。
`create()`メソッドにハッシュを渡します:

```javascript
import EmberObject from '@ember/object';

const Person = EmberObject.extend({
  helloWorld() {
    alert(`Hi, my name is ${this.get('name')}`);
  }
});

let tom = Person.create({
  name: 'Tom Dale'
});

tom.helloWorld(); // alerts "Hi, my name is Tom Dale"
```

<!--
Note that for performance reasons, while calling `create()` you cannot redefine an instance's
computed properties and should not redefine existing or define new methods. You should only set simple properties when calling
`create()`. If you need to define or redefine methods or computed
properties, create a new subclass and instantiate that.
-->

パフォーマンス上の理由から、`create()` を呼び出す際にインスタンスの算出プロパティを再定義することはできず、既存のメソッドの再定義、新しいメソッドの定義もできません。
`create()`を呼び出すときには、単純なプロパティのみを設定してください。
メソッドや算出プロパティを定義または再定義する必要がある場合は、新しいサブクラスを作成してインスタンス化してください。

<!--
By convention, properties or variables that hold classes are
PascalCased, while instances are not. So, for example, the variable
`Person` would point to a class, while `person` would point to an instance
(usually of the `Person` class). You should stick to these naming
conventions in your Ember applications.
-->

規約上、クラスを保持するプロパティまたは変数はアッパーキャメルケース(PascalCased)ですが、インスタンスはそうではありません。
たとえば、変数`Person`はクラスを指し、`person`は(`Person`クラスの)インスタンスを指します。 Emberアプリケーションでは、これらの命名規則に従うべきです。

<!--
### Initializing Instances
-->

### インスタンスの初期化

<!--
When a new instance is created, its [`init()`][6] method is invoked
automatically. This is the ideal place to implement setup required on new
instances:
-->

新しいインスタンスが作成されると、[`init()`][6]メソッドが自動で呼び出されます。
これは、インスタンスで必須なものをセットアップするのに理想的な場所です。

[6]: https://www.emberjs.com/api/ember/2.16/classes/EmberObject/methods/init?anchor=init

```js
import EmberObject from '@ember/object';

const Person = EmberObject.extend({
  init() {
    alert(`${this.get('name')}, reporting for duty!`);
  }
});

Person.create({
  name: 'Stefan Penner'
});

// alerts "Stefan Penner, reporting for duty!"
```

<!--
If you are subclassing a framework class, like `Ember.Component`, and you
override the `init()` method, make sure you call `this._super(...arguments)`!
If you don't, a parent class may not have an opportunity to do important
setup work, and you'll see strange behavior in your application.
-->

`Ember.Component`などのフレームワーククラスをサブクラス化し、`init()`メソッドをオーバーライドする場合は、`this._super(...arguments)`を**必ず呼び出してください！**
そうしないと、親クラスで重要なセットアップ作業を行う機会がなくなり、アプリケーションで奇妙な動作が発生します。

<!--
Arrays and objects defined directly on any `Ember.Object` are shared across all instances of that class.
-->

Ember.Objectで直接定義された配列とオブジェクトは、そのクラスのすべてのインスタンスで共有されます。

<!--
```js
import EmberObject from '@ember/object';

const Person = EmberObject.extend({
  shoppingList: ['eggs', 'cheese']
});

Person.create({
  name: 'Stefan Penner',
  addItem() {
    this.get('shoppingList').pushObject('bacon');
  }
});

Person.create({
  name: 'Robert Jackson',
  addItem() {
    this.get('shoppingList').pushObject('sausage');
  }
});

// Stefan and Robert both trigger their addItem.
// They both end up with: ['eggs', 'cheese', 'bacon', 'sausage']
```
-->

```js
import EmberObject from '@ember/object';

const Person = EmberObject.extend({
  shoppingList: ['卵', 'チーズ']
});

Person.create({
  name: 'ステファン ペナー',
  addItem() {
    this.get('shoppingList').pushObject('ベーコン');
  }
});

Person.create({
  name: 'ロバート ジャクソン',
  addItem() {
    this.get('shoppingList').pushObject('ソーセージ');
  }
});

// ステファンとロバートの両方がaddItemを実行すると、
// 二人のshoppingListは: ['卵', 'チーズ', 'ベーコン', 'ソーセージ']
// になります。
```

<!--
To avoid this behavior, it is encouraged to initialize those arrays and object properties during `init()`. Doing so ensures each instance will be unique.
-->

この挙動を避けるためには、`init()`の中でこれらの配列とオブジェクトのプロパティを初期化することをお勧めします。
これにより、各インスタンスが一意になります。

<!--
```js
import EmberObject from '@ember/object';

const Person = EmberObject.extend({
  init() {
    this.set('shoppingList', ['eggs', 'cheese']);
  }
});

Person.create({
  name: 'Stefan Penner',
  addItem() {
    this.get('shoppingList').pushObject('bacon');
  }
});

Person.create({
  name: 'Robert Jackson',
  addItem() {
    this.get('shoppingList').pushObject('sausage');
  }
});

// Stefan ['eggs', 'cheese', 'bacon']
// Robert ['eggs', 'cheese', 'sausage']
```
-->

```js
import EmberObject from '@ember/object';

const Person = EmberObject.extend({
  init() {
    this.set('shoppingList', ['卵', 'チーズ']);
  }
});

Person.create({
  name: 'ステファン ペナー',
  addItem() {
    this.get('shoppingList').pushObject('ベーコン');
  }
});

Person.create({
  name: 'ロバート ジャクソン',
  addItem() {
    this.get('shoppingList').pushObject('ソーセージ');
  }
});

// ステファン ['卵', 'チーズ', 'ベーコン']
// ロバート ['卵', 'チーズ', 'ソーセージ']
```

<!--
### Accessing Object Properties
-->

### オブジェクトプロパティへのアクセス

<!--
When accessing the properties of an object, use the [`get()`][7]
and [`set()`][8] accessor methods:
-->

オブジェクトのプロパティにアクセスするときは、[`get()`][7]
および[`set()`][8]アクセサメソッドを使用します。

[7]: https://www.emberjs.com/api/ember/2.16/classes/@ember%2Fobject/methods/get?anchor=get
[8]: https://www.emberjs.com/api/ember/2.16/classes/@ember%2Fobject/methods/set?anchor=set

```js
import EmberObject from '@ember/object';

const Person = EmberObject.extend({
  name: 'Robert Jackson'
});

let person = Person.create();

person.get('name'); // 'Robert Jackson'
person.set('name', 'Tobias Fünke');
person.get('name'); // 'Tobias Fünke'
```

<!--
Make sure to use these accessor methods; otherwise, computed properties won't
recalculate, observers won't fire, and templates won't update.
-->

これらのアクセサメソッドを必ず使用してください。
そうしないと、算出プロパティは再計算されず、オブザーバは発動せず、テンプレートは更新されません。
