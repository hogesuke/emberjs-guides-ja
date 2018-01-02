<!--
## What are Computed Properties?
-->

## 算出プロパティとは

<!--
In a nutshell, computed properties let you declare functions as properties.
You create one by defining a computed property as a function, which Ember will automatically call when you ask for the property.
You can then use it the same way you would any normal, static property.
-->

簡単に言えば、算出プロパティは、関数をプロパティとして宣言できることです。
算出プロパティを関数として定義して、Emberがプ​​ロパティを要求するときに自動的にその関数が呼び出されます。
算出プロパティは通常の静的なプロパティと同じ方法で使用できます。

<!--
It's super handy for taking one or more normal properties and transforming or manipulating their data to create a new value.
-->

算出プロパティは、1つ以上の通常のプロパティを取得し、データを変換または操作して新しい値を作成するのに便利です。

<!--
### Computed properties in action
-->

### 実際の算出プロパティ

<!--
We'll start with a simple example.
We have a `Person` object with `firstName` and `lastName` properties, but we also want a `fullName` property that joins the two names when either of them changes:
-->

簡単な例から始めましょう。
`firstName`プロパティと`lastName`プロパティを持つ`Person`オブジェクトを用意します。
どちらかが変更されたときに2つの名前を結合する`fullName`プロパティを定義しましょう。

```javascript
import EmberObject, { computed } from '@ember/object';

Person = EmberObject.extend({
  // these will be supplied by `create`
  firstName: null,
  lastName: null,

  fullName: computed('firstName', 'lastName', function() {
    let firstName = this.get('firstName');
    let lastName = this.get('lastName');

    return `${firstName} ${lastName}`;
  })
});

let ironMan = Person.create({
  firstName: 'Tony',
  lastName:  'Stark'
});

ironMan.get('fullName'); // "Tony Stark"
```

<!--
This declares `fullName` to be a computed property, with `firstName` and `lastName` as the properties it depends on.
The first time you access the `fullName` property, the function will be called and the results will be cached.
Subsequent access of `fullName` will read from the cache without calling the function.
Changing any of the dependent properties causes the cache to invalidate, so that the computed function runs again on the next access.
-->

上記のコードは、`fullName`が算出プロパティであり、`firstName`と`lastName`がそれが依存するプロパティとして宣言します。
最初に`fullName`プロパティにアクセスすると、関数が呼び出され、結果がキャッシュされます。
その後の`fullName`へのアクセスは、関数を呼び出さずにキャッシュから読み取ります。
依存プロパティのいずれかを変更すると、キャッシュが無効になり、計算された関数が次のアクセス時に再び実行されます。

<!--
### Multiple dependents on the same object
-->

### 同じオブジェクト上での複数依存

<!--
In the previous example, the `fullName` computed property depends on two other properties:
-->

前の例では、`fullName`算出プロパティは、他の2つのプロパティに依存しています。

```javascript
import Ember from 'ember':

…
  fullName: computed('firstName', 'lastName', function() {
    let firstName = this.get('firstName');
    let lastName = this.get('lastName');

    return `${firstName} ${lastName}`;
  })
…
```
<!--
We can also use a short-hand syntax called _brace expansion_ to declare the dependents.
You surround the dependent properties with braces (`{}`), and separate with commas, like so:
-->

また、Brace Expansion(ブレース展開)と呼ばれる簡潔な構文を使用して、依存を宣言することもできます。
依存しているプロパティを中括弧(`{}`)で囲み、カンマで区切ります。
次のようにします。

```javascript
import Ember from 'ember':

…
  fullName: computed('{firstName,lastName}', function() {
    let firstName = this.get('firstName');
    let lastName = this.get('lastName');

    return `${firstName} ${lastName}`;
  })
…
```

<!--
This is especially useful when you depend on properties of an object, since it allows you to replace:
-->

これは、オブジェクトのプロパティに依存する場合に特に便利です。
以下の書き方を、

```javascript
import EmberObject, { computed } from '@ember/object';

let obj = EmberObject.extend({
  baz: {foo: 'BLAMMO', bar: 'BLAZORZ'},

  something: computed('baz.foo', 'baz.bar', function() {
    return this.get('baz.foo') + ' ' + this.get('baz.bar');
  })
});
```

次のように置き換えることができます。

```javascript
import EmberObject, { computed } from '@ember/object';

let obj = EmberObject.extend({
  baz: {foo: 'BLAMMO', bar: 'BLAZORZ'},

  something: computed('baz.{foo,bar}', function() {
    return this.get('baz.foo') + ' ' + this.get('baz.bar');
  })
});
```

<!--
### Chaining computed properties
-->

### 算出プロパティの連結

<!--
You can use computed properties as values to create new computed properties.
Let's add a `description` computed property to the previous example,
and use the existing `fullName` property and add in some other properties:
-->

算出プロパティを値として使用して、新しい算出プロパティを作成できます。
前の例に算出プロパティ`description`を追加し、既存の`fullName`プロパティを使用して、他のプロパティを追加してみましょう。

```javascript
import EmberObject, { computed } from '@ember/object';

Person = EmberObject.extend({
  firstName: null,
  lastName: null,
  age: null,
  country: null,

  fullName: computed('firstName', 'lastName', function() {
    return `${this.get('firstName')} ${this.get('lastName')}`;
  }),

  description: computed('fullName', 'age', 'country', function() {
    return `${this.get('fullName')}; Age: ${this.get('age')}; Country: ${this.get('country')}`;
  })
});

let captainAmerica = Person.create({
  firstName: 'Steve',
  lastName: 'Rogers',
  age: 80,
  country: 'USA'
});

captainAmerica.get('description'); // "Steve Rogers; Age: 80; Country: USA"
```

<!--
### Dynamic updating
-->

### 動的更新

<!--
Computed properties, by default, observe any changes made to the properties they depend on and are dynamically updated when they're called.
Let's use computed properties to dynamically update.
-->

算出プロパティは、デフォルトでは、依存するプロパティに対する変更を監視し、呼び出されたときに動的に更新されます。
算出プロパティを使って動的に更新しましょう。

```javascript
captainAmerica.set('firstName', 'William');

captainAmerica.get('description'); // "William Rogers; Age: 80; Country: USA"
```

<!--
So this change to `firstName` was observed by `fullName` computed property, which was itself observed by the `description` property.
-->

`firstName`の変更は、`fullName`という算出プロパティによって監視されていることが、`description`プロパティで確認できました。

<!--
Setting any dependent property will propagate changes through any computed properties that depend on them, all the way down the chain of computed properties you've created.
-->

依存するプロパティを設定すると、作成した計算済みプロパティのチェーンの下にある、変更されたプロパティに依存する算出プロパティの変更が反映されます。

<!--
### Setting Computed Properties
-->

### 算出プロパティの設定

<!--
You can also define what Ember should do when setting a computed property.
If you try to set a computed property, it will be invoked with the key (property name), and the value you want to set it to.
You must return the new intended value of the computed property from the setter function.
-->

算出プロパティを設定するときに、Emberが何をすべきかを定義することもできます。
算出プロパティをセットする時は、キー(プロパティ名)と設定する値を引数に取ります。
setter関数から算出プロパティで意図した値を返す必要があります。

```javascript
import EmberObject, { computed } from '@ember/object';

Person = EmberObject.extend({
  firstName: null,
  lastName: null,

  fullName: computed('firstName', 'lastName', {
    get(key) {
      return `${this.get('firstName')} ${this.get('lastName')}`;
    },
    set(key, value) {
      let [firstName, lastName] = value.split(/\s+/);
      this.set('firstName', firstName);
      this.set('lastName',  lastName);
      return value;
    }
  })
});


let captainAmerica = Person.create();
captainAmerica.set('fullName', 'William Burnside');
captainAmerica.get('firstName'); // William
captainAmerica.get('lastName'); // Burnside
```

<!--
### Computed property macros
-->

### 算出プロパティマクロ

<!--
Some types of computed properties are very common.
Ember provides a number of computed property macros, which are shorter ways of expressing certain types of computed property.
-->

算出プロパティの種類にはよく使われるものがあります。
Emberは、算出プロパティを短く表現する算出プロパティマクロを多数提供しています。

<!--
In this example, the two computed properties are equivalent:
-->

この例では、2つの算出プロパティは等価です。

```javascript
import EmberObject, { computed } from '@ember/object';
import { equal } from '@ember/object/computed';

Person = EmberObject.extend({
  fullName: 'Tony Stark',

  isIronManLongWay: computed('fullName', function() {
    return this.get('fullName') === 'Tony Stark';
  }),

  isIronManShortWay: equal('fullName', 'Tony Stark')
});
```

<!--
To see the full list of computed property macros, have a look at
[the API documentation](https://www.emberjs.com/api/ember/2.16/modules/@ember%2Fobject)
-->

算出プロパティマクロの完全なリストを見るには、[APIドキュメント](https://www.emberjs.com/api/ember/2.16/modules/@ember%2Fobject)を見てください。
