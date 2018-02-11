<!--
The Ember Data store provides an interface for retrieving records of a single type.
-->

Ember DATAのストアは、一つのタイプのレコードを取得するためのインターフェイスを提供します。

<!--
### Retrieving a Single Record
-->

### レコードを1件取得する

<!--
Use [`store.findRecord()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Store/methods/findRecord?anchor=findRecord) to retrieve a record by its type and ID.
This will return a promise that fulfills with the requested record:
-->

タイプとIDでレコードを取得するには、[`store.findRecord()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Store/methods/findRecord?anchor=findRecord)を使用します。
要求されたレコードで解決するPromiseが返されます。

```javascript
let blogPost = this.get('store').findRecord('blog-post', 1); // => GET /blog-posts/1
```

<!--
Use [`store.peekRecord()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Store/methods/findRecord?anchor=peekRecord) to retrieve a record by its type and ID, without making a network request.
This will return the record only if it is already present in the store:
-->

ネットワークリクエストなしでタイプとIDでレコードを取得するには、[`store.peekRecord()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Store/methods/findRecord?anchor=peekRecord)を使用します。
これは、データがすでにストアに存在する場合にのみレコードを返します。

```javascript
let blogPost = this.get('store').peekRecord('blog-post', 1); // => no network request
```

<!--
### Retrieving Multiple Records
-->

### レコードを複数取得する

<!--
Use [`store.findAll()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Store/methods/findAll?anchor=findAll) to retrieve all of the records for a given type:
-->

指定されたタイプのレコードを全件取得するには、[`store.findAll()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Store/methods/findAll?anchor=findAll)を使用します。

```javascript
let blogPosts = this.get('store').findAll('blog-post'); // => GET /blog-posts
```

<!--
Use [`store.peekAll()`](http://emberjs.com/api/data/classes/DS.Store.html#method_peekAll) to retrieve all of the records for a given type that are already loaded into the store, without making a network request:
-->

ネットワークリクエストなしで取得するには、[`store.peekAll()`](http://emberjs.com/api/data/classes/DS.Store.html#method_peekAll)を使用します。
ストアにあるデータから、指定されたタイプのレコードを全件返します。

```javascript
let blogPosts = this.get('store').peekAll('blog-post'); // => no network request
```

<!--
`store.findAll()` returns a `DS.PromiseArray` that fulfills to a `DS.RecordArray` and `store.peekAll` directly returns a `DS.RecordArray`.
-->

`store.findAll()`は`DS.RecordArray`を解決する`DS.PromiseArray`を返し、`store.peekAll`は`DS.RecordArray`を直接返します。

<!--
It's important to note that `DS.RecordArray` is not a JavaScript array, it's an object that implements [`Ember.Enumerable`](http://emberjs.com/api/classes/Ember.Enumerable.html).
This is important because, for example, if you want to retrieve records by index,
the `[]` notation will not work--you'll have to use `objectAt(index)` instead.
-->

`DS.RecordArray`はJavaScript配列ではなく、[`Ember.Enumerable`](http://emberjs.com/api/classes/Ember.Enumerable.html)を実装しているオブジェクトであることに注意してください。
例えば、インデックスでレコードを取得する場合は、`[]`を使うことはできず、代わりに`objectAt(index)`を使用する必要があります。

<!--
### Querying for Multiple Records
-->

### クエリーによる複数件のレコード検索

<!--
Ember Data provides the ability to query for records that meet certain criteria.
Calling [`store.query()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Store/methods/query?anchor=query) will make a `GET` request with the passed object serialized as query params.
This method returns a `DS.PromiseArray` in the same way as `findAll`.
-->
Ember Dataには、特定の条件を満たすレコードを検索する機能があります。
[`store.query()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Store/methods/query?anchor=query)を呼び出すと、渡されたオブジェクトをクエリパラメータとしてシリアルライズして`GET`リクエストを行います。
このメソッドは、`findAll`と同様に`DS.PromiseArray`を返します。

<!--
For example, we could search for all `person` models who have the name of
`Peter`:
-->

例えば、`Peter`の名前を持つすべての`person`モデルを検索する場合、以下のように書きます。

```javascript
// GET to /persons?filter[name]=Peter
this.get('store').query('person', {
  filter: {
    name: 'Peter'
  }
}).then(function(peters) {
  // Do something with `peters`
});
```

<!--
### Querying for A Single Record
-->

### クエリーによる1件のレコード検索

<!--
If you are using an adapter that supports server requests capable of returning a single model object,
Ember Data provides a convenience method [`store.queryRecord()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Store/methods/query?anchor=queryRecord)that will return a promise that resolves with that single record.
The request is made via a method `queryRecord()` defined by the adapter.
-->

単一のモデルオブジェクトを返すサーバーリクエストをサポートするアダプターを使用している場合、Ember Dataでは[`store.queryRecord()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Store/methods/query?anchor=queryRecord)という便利なメソッドが使えます。返し値は単一のレコードで解決するPromiseです。
リクエストは、アダプタで定義されている`queryRecord()`メソッドを介して行われます。

<!--
For example, if your server API provides an endpoint for the currently logged in user:
-->

例えば、サーバーAPIが現在ログインしているユーザーのエンドポイントを、以下にのように提供しているとします。


```text
// GET /api/current_user
{
  user: {
    id: 1234,
    username: 'admin'
  }
}
```

<!--
and the adapter for the `User` model defines a `queryRecord()` method that targets that endpoint:
-->

`User`モデル用のアダプタは、そのエンドポイントをターゲットとする`queryRecord()`メソッドを定義します。

```app/adapters/user.js
import DS from 'ember-data';
import $ from 'jquery';

export default DS.Adapter.extend({
  queryRecord(store, type, query) {
    return $.getJSON('/api/current_user');
  }
});
```

<!--
then calling [`store.queryRecord()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Store/methods/query?anchor=queryRecord) will retrieve that object from the server:
-->

[`store.queryRecord()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Store/methods/query?anchor=queryRecord)を呼び出すと、サーバーからそのオブジェクトが取得されます。

```javascript
store.queryRecord('user', {}).then(function(user) {
  let username = user.get('username');
  console.log(`Currently logged in as ${username}`);
});
```

<!--
As in the case of `store.query()`, a query object can also be passed to `store.queryRecord()` and is available for the adapter's `queryRecord()` to use to qualify the request.
However the adapter must return a single model object, not an array containing one element,
otherwise Ember Data will throw an exception.
-->

`store.query()`の場合と同様に、クエリーオブジェクトは`store.queryRecord()`に渡すこともでき、アダプターの`queryRecord()`でリクエストの条件の指定に使用できます。
ただし、アダプタは1つの要素オブジェクトを含む配列ではなく、単一のモデルオブジェクトを返す必要があります。
単一でない場合、Ember Dataは例外を投げます。

<!--
Note that Ember's default [JSON API adapter](https://www.emberjs.com/api/ember-data/2.16/classes/DS.JSONAPIAdapter) does not provide the functionality needed to support `queryRecord()` directly as it relies on REST request definitions that return result data in the form of an array.
-->

Emberのデフォルトの[JSON APIアダプター](https://www.emberjs.com/api/ember-data/2.16/classes/DS.JSONAPIAdapter)は、結果のデータを配列形式で返すREST要求定義に依存しているため、`queryRecord()`を直接サポートするための必要な機能は提供していません。

<!--
If your server API or your adapter only provides array responses but you wish to retrieve just a single record, you can alternatively use the `query()` method as follows:
-->

サーバーAPIまたはアダプタで配列の応答しか提供されていない場合で、単一のレコードを取得したい場合は、以下のように`query()`メソッドを使用することで解決できます。

```javascript
// GET to /users?filter[email]=tomster@example.com
tom = store.query('user', {
  filter: {
    email: 'tomster@example.com'
  }
}).then(function(users) {
  return users.get("firstObject");
});
```
