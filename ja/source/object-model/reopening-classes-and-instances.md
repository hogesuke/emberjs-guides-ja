<!--
You don't need to define a class all at once. You can reopen a class and
define new properties using the
[`reopen()`](http://emberjs.com/api/classes/Ember.Object.html#method_reopen)
method.
-->

一度にクラスを定義する必要はありません。 [`reopen()`](http://emberjs.com/api/classes/Ember.Object.html#method_reopen)メソッドを使用すると、クラスを再度開き、新しいプロパティを定義できます。

```javascript
Person.reopen({
  isPerson: true
});

Person.create().get('isPerson'); // true
```

<!--
When using `reopen()`, you can also override existing methods and
call `this._super`.
-->

`reopen()`を使用するときは、既存のメソッドをオーバーライドして`this._super`を呼び出すこともできます。

<!--
```javascript
Person.reopen({
  // override `say` to add an ! at the end
  say(thing) {
    this._super(thing + '!');
  }
});
```
-->

```javascript
Person.reopen({
  // `say`メソッドを上書きして末尾に!を追加する
  say(thing) {
    this._super(thing + '!');
  }
});
```

<!--
`reopen()` is used to add instance methods and properties that are shared 
across all instances of a class. It does not add
methods and properties to a particular instance of a class as in vanilla JavaScript (without using prototype).
-->

`reopen()`は、クラスのすべてのインスタンスで共有するインスタンスメソッドとプロパティを追加するために使用します。
(prototypeを使わない場合の)バニラJavaScriptのように、クラスの特定のインスタンスにメソッドやプロパティを追加することはありません。

<!--
But when you need to add static methods or static properties to the class itself
you can use [`reopenClass()`](http://emberjs.com/api/classes/Ember.Object.html#method_reopenClass).
-->

しかし、静的メソッドや静的プロパティをクラス自体に追加する必要がある場合は、[`reopenClass()`](http://emberjs.com/api/classes/Ember.Object.html#method_reopenClass)を使用できます。

<!--
```javascript
// add static property to class
Person.reopenClass({
  isPerson: false
});
// override property of existing and future Person instances
Person.reopen({
  isPerson: true
});

Person.isPerson; // false - because it is static property created by `reopenClass`
Person.create().get('isPerson'); // true
```
-->

```javascript
// 静的プロパティをクラスに追加
Person.reopenClass({
  isPerson: false
});
// 既存のインスタンスと今後作成されるインスタンスのプロパティを上書き
Person.reopen({
  isPerson: true
});

Person.isPerson; // false - reopenClassで作成された静的プロパティのため
Person.create().get('isPerson'); // true
```
