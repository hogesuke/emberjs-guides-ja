<!--
In Ember.js, an enumerable is any object that contains a number of child
objects, and which allows you to work with those children using the
[Ember.Enumerable](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable) API. The most common
enumerable in the majority of apps is the native JavaScript array, which
Ember.js extends to conform to the enumerable interface.
-->

Ember.jsでは、列挙型(Enumerable)は、多数の子オブジェクトを含む任意のオブジェクトであり、[Ember.Enumerable](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable)を使用して子オブジェクトを操作できます。
アプリケーションで最も一般的な列挙型は、Ember.jsがEnumerableのインターフェイスに準拠するように拡張しているネイティブJavaScriptの配列(Array)です。

<!--
By providing a standardized interface for dealing with enumerables,
Ember.js allows you to completely change the way your underlying data is
stored without having to modify the other parts of your application that
access it.
-->

列挙型を扱うための標準化されたインタフェースを提供することにより、Ember.jsは、アプリケーション内で列挙型にアクセスする部分を変更することなく、基礎となるデータの格納方法を完全に変更することができます。

<!--
The enumerable API follows ECMAScript specifications as much as
possible. This minimizes incompatibility with other libraries, and
allows Ember.js to use the native browser implementations in arrays
where available.
-->

列挙可能なAPIは、可能な限りECMAScript仕様に準拠しています。 これにより、他のライブラリとの非互換性が最小限に抑えられており、Ember.jsはブラウザのネイティブ実装が利用できる場合は利用します。


<!--
## Use of Observable Methods and Properties
-->

## オブザーバー用のメソッドとプロパティ

<!--
In order for Ember to observe when you make a change to an enumerable, you need
to use special methods that `Ember.Enumerable` provides. For example, if you add
an element to an array using the standard JavaScript method `push()`, Ember will
not be able to observe the change, but if you use the enumerable method
`pushObject()`, the change will propagate throughout your application.
-->

列挙型の変更をEmberが監視できるようにするには、`Ember.Enumerable`が提供する特別なメソッドを使用する必要があります。
たとえば、標準のJavaScriptメソッド`push()`を使用して配列に要素を追加すると、Emberは変更を監視できませんが、Enumerableの`pushObject()`メソッドを使用すると、アプリケーション全体に変更が反映されます。

<!--
Here is a list of standard JavaScript array methods and their observable
enumerable equivalents:
-->

以下は、標準のJavaScript配列メソッドとオブザーバーを使う場合のメソッドの表です。

<!--
<table>
  <thead>
    <tr><th>Standard Method</th><th>Observable Equivalent</th></tr>
  </thead>
  <tbody>
    <tr><td>pop</td><td>popObject</td></tr>
    <tr><td>push</td><td>pushObject</td></tr>
    <tr><td>reverse</td><td>reverseObjects</td></tr>
    <tr><td>shift</td><td>shiftObject</td></tr>
    <tr><td>unshift</td><td>unshiftObject</td></tr>
  </tbody>
</table>
-->

<table>
  <thead>
    <tr><th>標準メソッド</th><th>オブザーバー用メソッド</th></tr>
  </thead>
  <tbody>
    <tr><td>pop</td><td>popObject</td></tr>
    <tr><td>push</td><td>pushObject</td></tr>
    <tr><td>reverse</td><td>reverseObjects</td></tr>
    <tr><td>shift</td><td>shiftObject</td></tr>
    <tr><td>unshift</td><td>unshiftObject</td></tr>
  </tbody>
</table>

<!--
Additionally, to retrieve the first and last objects in an array
in an observable fashion, you should use `myArray.get('firstObject')` and
`myArray.get('lastObject')`, respectively.
-->

さらに、配列内の最初と最後のオブジェクトをオブザーブ可能な形式で取得するには、それぞれ`myArray.get('firstObject')`と`myArray.get('lastObject')`を使用する必要があります。

<!--
## API Overview
-->

## APIの概要

<!--
In the rest of this guide, we'll explore some of the most common enumerable
conveniences. For the full list, please see the [Ember.Enumerable API
reference documentation.](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable)
-->

このガイドの残りの部分では、よく使う便利な機能をいくつか紹介します。
全部の機能については、[Ember.Enumerable APIのリファレンスドキュメント](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable)を参照してください。

<!--
### Iterating Over an Enumerable
-->

### Enumerableを繰り返し処理する

<!--
To enumerate all the values of an enumerable object, use the [`forEach()`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/methods/forEach?anchor=forEach)
method:
-->

列挙型のオブジェクトのすべての値を列挙するには、[`forEach()`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/methods/forEach?anchor=forEach)メソッドを使用します。


```javascript
let food = ['Poi', 'Ono', 'Adobo Chicken'];

food.forEach((item, index) => {
  console.log(`Menu Item ${index+1}: ${item}`);
});

// Menu Item 1: Poi
// Menu Item 2: Ono
// Menu Item 3: Adobo Chicken
```

<!--
### First and Last Objects
-->

### 最初と最後のオブジェクト

<!--
All enumerables expose [`firstObject`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/properties/firstObject?anchor=firstObject) and [`lastObject`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/properties/lastObject?anchor=lastObject) properties
that you can bind to.
-->

すべての列挙型は、バインドできる[`firstObject`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/properties/firstObject?anchor=firstObject)および[`lastObject`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/properties/lastObject?anchor=lastObject)プロパティを持っています。



```javascript
let animals = ['rooster', 'pig'];

animals.get('lastObject');
//=> "pig"

animals.pushObject('peacock');

animals.get('lastObject');
//=> "peacock"
```

<!--
### Map
-->

### マップ

<!--
You can easily transform each item in an enumerable using the
[`map()`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/methods/map?anchor=map) method, which creates a new array with results of calling a
function on each item in the enumerable.
-->

[`map()`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/methods/map?anchor=map)メソッドを使用して列挙型の各アイテムを簡単に変換することができます。これは、列挙型の各アイテムに関数を実行した結果の新しい配列を作成します。


```javascript
let words = ['goodbye', 'cruel', 'world'];

let emphaticWords = words.map(item => `${item}!`);
//=> ["goodbye!", "cruel!", "world!"]
```

<!--
If your enumerable is composed of objects, there is a [`mapBy()`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/methods/mapBy?anchor=mapBy)
method that will extract the named property from each of those objects
in turn and return a new array:
-->

列挙型がオブジェクトで構成されている場合、[`mapBy()`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/methods/mapBy?anchor=mapBy)メソッドがあります
このメソッドは、各オブジェクトから名前付きプロパティを順番に抽出し、新しい配列を返します。


```javascript
import EmberObject from '@ember/object';

let hawaii = EmberObject.create({
  capital: 'Honolulu'
});

let california = EmberObject.create({
  capital: 'Sacramento'
});

let states = [hawaii, california];

states.mapBy('capital');
//=> ["Honolulu", "Sacramento"]
```

<!--
### Filtering
-->

### フィルタリング

<!--
Another common task to perform on an enumerable is to take the
enumerable as input, and return an Array after filtering it based on
some criteria.
-->

ほかに列挙型に対してよく実行するタスクは、列挙型を入力として受け取り、いくつかの条件に基づいてフィルタリングした後に配列を返すことです。

<!--
For arbitrary filtering, use the [`filter()`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/methods/filter?anchor=filter) method.  The filter method
expects the callback to return `true` if Ember should include it in the
final Array, and `false` or `undefined` if Ember should not.
-->

任意のフィルタリングを行うには、[`filter()`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/methods/filter?anchor=filter)メソッドを使用します。
コールバックが`true`を返すと、配列に対象を含めます。`false`または` undefined`の場合は除外します。

```javascript
let arr = [1, 2, 3, 4, 5];

arr.filter((item, index, self) => item < 4);

//=> [1, 2, 3]
```

<!--

When working with a collection of Ember objects, you will often want to filter a set of objects based upon the value of some property. The [`filterBy()`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/methods/filterBy?anchor=filterBy) method provides a shortcut.
-->

Emberオブジェクトの配列を扱う際、しばしば、プロパティの値に基づいてオブジェクトのセットをフィルタリングする場面が出てきます。
[`filterBy()`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/methods/filterBy?anchor=filterBy)メソッドはショートカットを提供します。


```javascript
import EmberObject from '@ember/object';

Todo = EmberObject.extend({
  title: null,
  isDone: false
});

let todos = [
  Todo.create({ title: 'Write code', isDone: true }),
  Todo.create({ title: 'Go to sleep' })
];

todos.filterBy('isDone', true);

// returns an Array containing only items with `isDone == true`
```

<!--
If you only want to return the first matched value, rather than an Array
containing all of the matched values, you can use [`find()`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/methods/find?anchor=find) and [`findBy()`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/methods/findBy?anchor=findBy),
which work like `filter()` and `filterBy()`, but return only one item.
-->

一致したすべての値を含む配列ではなく、最初に一致した値のみを返す場合は、`filter()`および`filterBy()`のように機能する[`find()`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/methods/find?anchor=find)および[`findBy()`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/methods/findBy?anchor=findBy)を使用できます。


<!--
### Aggregate Information (Every or Any)
-->

### 情報の集計(EveryとAny)

<!--
To find out whether every item in an enumerable matches some condition, you can
use the [`every()`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/methods/every?anchor=every) method:
-->

列挙型のすべての項目がある条件に一致するかどうかを調べるには、[`every()`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/methods/every?anchor=every)メソッドを使用します。

```javascript
import EmberObject from '@ember/object';

Person = EmberObject.extend({
  name: null,
  isHappy: false
});

let people = [
  Person.create({ name: 'Yehuda', isHappy: true }),
  Person.create({ name: 'Majd', isHappy: false })
];

people.every((person, index, self) => person.get('isHappy'));

//=> false
```

<!--
To find out whether at least one item in an enumerable matches some condition,
you can use the [`any()`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/methods/any?anchor=any) method:
-->

列挙型の中の少なくとも1つの項目がある条件と一致するかどうかを調べるには、[`any()`](https://emberjs.com/api/ember/2.15/classes/Ember.Enumerable/methods/any?anchor=any)メソッドを使用します。


```javascript
people.any((person, index, self) => person.get('isHappy'));

//=> true
```

<!--
Like the filtering methods, the `every()` and `any()` methods have
analogous `isEvery()` and `isAny()` methods.
-->

フィルタリングメソッドと同様に、`every()`メソッドと`any()`メソッドには、`isEvery()`メソッドと`isAny()`メソッドがあります。

```javascript
people.isEvery('isHappy', true); // false
people.isAny('isHappy', true);  // true
```
