<!--
Along with the records returned from your store, you'll likely need to handle some kind of metadata. *Metadata* is data that goes along with a specific *model* or *type* instead of a record.
-->

あなたの店から返されたレコードに加えて、ある種のメタデータを扱う必要があるでしょう。
メタデータとは、レコードではなく特定のモデルまたはタイプに沿ったデータです。

<!--
Pagination is a common example of using metadata. Imagine a blog with far more posts than you can display at once. You might query it like so:
-->

ページネーションはメタデータを使用する一般的な例です。 一度に表示できるよりもはるかに多くのブログを想像してみてください。 次のようにクエリを実行するかもしれません。

```js
let result = this.get('store').query('post', {
  limit: 10,
  offset: 0
});
```

<!--
To get different *pages* of data, you'd simply change your offset in increments of 10. So far, so good. But how do you know how many pages of data you have? Your server would need to return the total number of records as a piece of metadata.
-->

データの異なるページを取得するには、オフセットを10ずつ増減するだけです。
しかし、どれくらいのページのデータがあるかをどのように知っていますか？
サーバーはメタデータとしてレコードの総数を返す必要があります。

<!--
Each serializer will expect the metadata to be returned differently. For example, Ember Data's JSON deserializer looks for a `meta` key:
-->

各シリアライザは、メタデータが異なる方法で返されることを期待します。
たとえば、Ember DataのJSONデシリアライザはメタキーを探します。

```js
{
  "post": {
    "id": 1,
    "title": "Progressive Enhancement is Dead",
    "comments": ["1", "2"],
    "links": {
      "user": "/people/tomdale"
    },
    // ...
  },

  "meta": {
    "total": 100
  }
}
```

<!--
Regardless of the serializer used, this metadata is extracted from the response. You can then read it with `.get('meta')`.
-->

使用されるシリアライザに関係なく、このメタデータはレスポンスから抽出されます。
その後、`.get('meta')`で読むことができます。

<!--
This can be done on the result of a `store.query()` call:
-->

これは、`store.query()`呼び出しの結果として実行できます。

```js
store.query('post').then((result) => {
  let meta = result.get('meta');
});
```

<!--
On a belongsTo relationship:
-->

belongsToの場合、

```js
let post = store.peekRecord('post', 1);

post.get('author').then((author) => {
  let meta = author.get('meta');
});
```

<!--
Or on a hasMany relationship:
-->

またはhasManyで、

```js
let post = store.peekRecord('post', 1);

post.get('comments').then((comments) => {
  let meta = comments.get('meta');
});
```

<!--
After reading it, `meta.total` can be used to calculate how many pages of posts you'll have.
-->

それを読んだら、`meta.total`を使って、あなたが持つ投稿のページ数を計算することができます。

<!--
To use the `meta` data outside of the `model` hook, you need to return it:
-->

`model`フックの外で`meta`データを使用するには、それを返す必要があります。

```app/routes/users.js
import Router from '@ember/routing/route';

export default Route.extend({
  model() {
    return this.store.query('user', {}).then((results) => {
      return {
        users: results,
        meta: results.get('meta')
      };
    });
  },
  setupController(controller, { users, meta }) {
    this._super(controller, users);
    controller.set('meta', meta);
  }
});
```

<!--
To customize metadata extraction, check out the documentation for your serializer.
-->

メタデータ抽出をカスタマイズするには、シリアライザのドキュメントを参照してください。
