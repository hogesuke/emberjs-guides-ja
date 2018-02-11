<!--
## Creating Records
-->

## レコードの作成

<!--
You can create records by calling the
[`createRecord()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Store/methods/createRecord?anchor=createRecord)
method on the store.
-->

ストアの[`createRecord()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Store/methods/createRecord?anchor=createRecord)メソッドを呼び出すと、レコードを作成できます。

```js
store.createRecord('post', {
  title: 'Rails is Omakase',
  body: 'Lorem ipsum'
});
```

<!--
The store object is available in controllers and routes using `this.get('store')`.
-->

ストアオブジェクトは、`this.get('store')`を使用してコントローラとルートで使用できます。

<!--
## Updating Records
-->

## レコードの更新

<!--
Making changes to Ember Data records is as simple as setting the attribute you
want to change:
-->

Ember Dataレコードの変更は、変更したい属性を設定するだけです。

```js
this.get('store').findRecord('person', 1).then(function(tyrion) {
  // ...after the record has loaded
  tyrion.set('firstName', 'Yollo');
});
```

<!--
All of the Ember.js conveniences are available for
modifying attributes. For example, you can use `Ember.Object`'s
[`incrementProperty`](http://emberjs.com/api/classes/Ember.Object.html#method_incrementProperty) helper:
-->

Ember.jsの便利さは属性が変更可能なところです。
例えば、`Ember.Object`の[`incrementProperty`](http://emberjs.com/api/classes/Ember.Object.html#method_incrementProperty)ヘルパーを使用できます。

```js
person.incrementProperty('age'); // Happy birthday!
```

<!--
## Persisting Records
-->

## レコードの保存

<!--
Records in Ember Data are persisted on a per-instance basis.
Call [`save()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Model/methods/save?anchor=save)
on any instance of `DS.Model` and it will make a network request.
-->

Ember Dataのレコードは、インスタンスごとに保存されます。
DS.Modelのインスタンスで[`save()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Model/methods/save?anchor=save)を呼び出すと、ネットワークリクエストが発生します。

<!--
Ember Data takes care of tracking the state of each record for
you. This allows Ember Data to treat newly created records differently
from existing records when saving.
-->

Ember Dataは各レコードの状態を追跡します。
これにより、Ember Dataは、新しく作成されたレコードを、保存の際に既存のレコードとは異なる方法で扱います。

<!--
By default, Ember Data will `POST` newly created records to their type url.
-->

デフォルトでは、Ember Dataは新しく作成したレコードをそのタイプのURLに`POST`します。

```javascript
let post = store.createRecord('post', {
  title: 'Rails is Omakase',
  body: 'Lorem ipsum'
});

post.save(); // => POST to '/posts'
```

<!--
Records that already exist on the backend are updated using the HTTP `PATCH` verb.
-->

すでにバックエンドに存在するレコードは、HTTPの`PATCH`メソッドで更新されます。

```javascript
store.findRecord('post', 1).then(function(post) {
  post.get('title'); // => "Rails is Omakase"

  post.set('title', 'A new post');

  post.save(); // => PATCH to '/posts/1'
});
```

<!--
You can tell if a record has outstanding changes that have not yet been
saved by checking its
[`hasDirtyAttributes`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Model/properties/hasDirtyAttributes?anchor=hasDirtyAttributes)
property. You can also see what parts of
the record were changed and what the original value was using the
[`changedAttributes()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Model/methods/changedAttributes?anchor=changedAttributes)
method. `changedAttributes` returns an object, whose keys are the changed
properties and values are an array of values `[oldValue, newValue]`.
-->

レコードの[`hasDirtyAttributes`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Model/properties/hasDirtyAttributes?anchor=hasDirtyAttributes)プロパティをチェックすることで、まだ保存されていない変更があるかどうかを知ることができます。
レコードのどの部分が変更されたか、変更前の元の値は[`changedAttributes()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Model/methods/changedAttributes?anchor=changedAttributes)メソッドで確認できます。
`changedAttributes`は、変更されたプロパティをキーとするオブジェクトを返し、値は`[oldValue, newValue]`の値の配列です。

```js
person.get('isAdmin');            // => false
person.get('hasDirtyAttributes'); // => false
person.set('isAdmin', true);
person.get('hasDirtyAttributes'); // => true
person.changedAttributes();       // => { isAdmin: [false, true] }
```

<!--
At this point, you can either persist your changes via `save()` or you can roll
back your changes. Calling
[`rollbackAttributes()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Model/methods/rollbackAttributes?anchor=rollbackAttributes)
for a saved record reverts all the `changedAttributes` to their original value.
If the record `isNew` it will be removed from the store.
-->

この時点で、`save()`を使って変更を保持することも、変更をロールバックすることもできます。
保存されたレコードの[`rollbackAttributes()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Model/methods/rollbackAttributes?anchor=rollbackAttributes)を呼び出すと、すべての`changedAttributes`が元の値に戻ります。
レコードが新しい(`isNew`)場合は、ストアから削除されます。

```js
person.get('hasDirtyAttributes'); // => true
person.changedAttributes();       // => { isAdmin: [false, true] }

person.rollbackAttributes();

person.get('hasDirtyAttributes'); // => false
person.get('isAdmin');            // => false
person.changedAttributes();       // => {}
```

<!--
## Handling Validation Errors
-->

## バリデーションエラーのハンドリング

<!--
If the backend server returns validation errors after trying to save, they will
be available on the `errors` property of your model. Here's how you might display
the errors from saving a blog post in your template:
-->

バックエンドサーバーにデータを保存する際にバリデーションエラーが返ってきた場合は、モデルの`errors`プロパティーが利用できます。
ブログ記事の保存で、テンプレートでエラーを表示するには以下のようにします。

```handlebars
{{#each post.errors.title as |error|}}
  <div class="error">{{error.message}}</div>
{{/each}}
{{#each post.errors.body as |error|}}
  <div class="error">{{error.message}}</div>
{{/each}}
```

## Promises

<!--
[`save()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Model/methods/save?anchor=save) returns
a promise, which makes it easy to asynchronously handle success and failure
scenarios.  Here's a common pattern:
-->
[`save()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Model/methods/save?anchor=save)はPromiseを返します。
成功と失敗のシナリオを非同期に処理できるようにします。
よくあるパターンは次のとおりです。

```javascript
let post = store.createRecord('post', {
  title: 'Rails is Omakase',
  body: 'Lorem ipsum'
});

let self = this;

function transitionToPost(post) {
  self.transitionToRoute('posts.show', post);
}

function failure(reason) {
  // handle the error
}

post.save().then(transitionToPost).catch(failure);

// => POST to '/posts'
// => transitioning to posts.show route
```

<!--
## Deleting Records
-->

## レコードの削除

<!--
Deleting records is as straightforward as creating records. Call [`deleteRecord()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Model/methods/deleteRecord?anchor=deleteRecord)
on any instance of `DS.Model`. This flags the record as `isDeleted`. The
deletion can then be persisted using `save()`.  Alternatively, you can use
the [`destroyRecord`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Model/methods/deleteRecord?anchor=destroyRecord) method to delete and persist at the same time.
-->

レコードの削除は、レコードの作成と同じくらい簡単です。
`DS.Model`の任意のインスタンスで[`deleteRecord()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Model/methods/deleteRecord?anchor=deleteRecord)を呼び出します。
これはレコードの`isDeleted`フラグを立てます。
削除は、`save()`を使用して永続化することができます。
また、[`destroyRecord`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Model/methods/deleteRecord?anchor=destroyRecord)メソッドを使用して、削除と永続化を同時に行うこともできます。


```js
store.findRecord('post', 1, { backgroundReload: false }).then(function(post) {
  post.deleteRecord();
  post.get('isDeleted'); // => true
  post.save(); // => DELETE to /posts/1
});

// OR
store.findRecord('post', 2, { backgroundReload: false }).then(function(post) {
  post.destroyRecord(); // => DELETE to /posts/2
});
```

<!--
The `backgroundReload` option is used to prevent the fetching of the destroyed record, since [`findRecord()`][findRecord] automatically schedules a fetch of the record from the adapter.
-->

`backgroundReload`オプションは、破棄されたレコードのフェッチを防ぐために使用します。
[findRecord()][findRecord]が自動的にアダプタからレコードのフェッチをスケジュールするためです。


[findRecord]: <https://www.emberjs.com/api/ember-data/2.16/classes/DS.Store/methods/findRecord?anchor=findRecord>
