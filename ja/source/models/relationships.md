<!--
Ember Data includes several built-in relationship types to help you
define how your models relate to each other.
-->

Ember Dataは、モデルの相互関係の定義を支援するために、組み込みの関連付け(リレーションシップ)タイプをいくつか用意しています。

<!--
### One-to-One
-->

### 1対1

<!--
To declare a one-to-one relationship between two models, use
`DS.belongsTo`:
-->

2つのモデル間に1対1の関係を宣言するには、`DS.belongsTo`を使用します。

```app/models/user.js
import DS from 'ember-data';

export default DS.Model.extend({
  profile: DS.belongsTo('profile')
});
```

```app/models/profile.js
import DS from 'ember-data';

export default DS.Model.extend({
  user: DS.belongsTo('user')
});
```


<!--
### One-to-Many
-->

### 1対多

<!--
To declare a one-to-many relationship between two models, use
`DS.belongsTo` in combination with `DS.hasMany`, like this:
-->

2つのモデルの間に1対多の関係を宣言するには、`DS.hasMany`と組み合わせて`DS.belongsTo`を使用します。

```app/models/blog-post.js
import DS from 'ember-data';

export default DS.Model.extend({
  comments: DS.hasMany('comment')
});
```

```app/models/comment.js
import DS from 'ember-data';

export default DS.Model.extend({
  blogPost: DS.belongsTo('blog-post')
});
```

<!--
### Many-to-Many
-->

### 多対多

<!--
To declare a many-to-many relationship between two models, use
`DS.hasMany`:
-->

2つのモデルの間に多対多の関係を宣言するには、`DS.hasMany`を使用します。

```app/models/blog-post.js
import DS from 'ember-data';

export default DS.Model.extend({
  tags: DS.hasMany('tag')
});
```

```app/models/tag.js
import DS from 'ember-data';

export default DS.Model.extend({
  blogPosts: DS.hasMany('blog-post')
});
```

<!--
### Explicit Inverses
-->

### 逆関係の明示

<!--
Ember Data will do its best to discover which relationships map to one
another. In the one-to-many code above, for example, Ember Data can figure out that
changing the `comments` relationship should update the `blogPost`
relationship on the inverse because `blogPost` is the only relationship to
that model.
-->

Ember Dataは、どの関係が互いにマップされているかを突き止めるために最善を尽くします。
例えば、上の1対多のコードでは、`blogPost`がそのモデルとの唯一の関係であるため、`comments`関係を変更すると、逆の`blogPost`関係も更新する必要があることを、Ember Dataは知ることができます。

<!--
However, sometimes you may have multiple `belongsTo`/`hasMany`s for
the same type. You can specify which property on the related model is
the inverse using `DS.belongsTo` or `DS.hasMany`'s `inverse`
option. Relationships without an inverse can be indicated as such by
including `{ inverse: null }`.
-->

しかし、同じタイプの複数の`belongsTo`/`hasManys`を持つことが時々あります。
その場合、`DS.belongsTo`や`DS.hasMany`の`inverse`オプションを使用して、関連モデルのどのプロパティを逆にするかを指定できます。
逆のない関係は、`{inverse：null}`を含めることでそのように示すことができます。

```app/models/comment.js
import DS from 'ember-data';

export default DS.Model.extend({
  onePost: DS.belongsTo('blog-post', { inverse: null }),
  twoPost: DS.belongsTo('blog-post'),
  redPost: DS.belongsTo('blog-post'),
  bluePost: DS.belongsTo('blog-post')
});
```

```app/models/blog-post.js
import DS from 'ember-data';

export default DS.Model.extend({
  comments: DS.hasMany('comment', {
    inverse: 'redPost'
  })
});
```

<!--
### Reflexive Relations
-->

### 反射関係(再帰)

<!--
When you want to define a reflexive relation (a model that has a relationship to
itself), you must explicitly define the inverse relationship. If there
is no inverse relationship then you can set the inverse to `null`.
-->

反射的関係(自身との関係を持つモデル)を定義する場合は、逆関係を明示的に定義する必要があります。
逆関係がない場合は、inverseを`null`に設定できます。

<!--
Here's an example of a one-to-many reflexive relationship:
-->

以下は、1対多の反射的関係の例です。

```app/models/folder.js
import DS from 'ember-data';

export default DS.Model.extend({
  children: DS.hasMany('folder', { inverse: 'parent' }),
  parent: DS.belongsTo('folder', { inverse: 'children' })
});
```

<!--
Here's an example of a one-to-one reflexive relationship:
-->

以下は、1対1の反射的関係の例です。

```app/models/user.js
import DS from 'ember-data';

export default DS.Model.extend({
  name: DS.attr('string'),
  bestFriend: DS.belongsTo('user', { inverse: 'bestFriend' }),
});
```

<!--
You can also define a reflexive relationship that doesn't have an inverse:
-->

逆の関係がない反射関係を定義することもできます。

```app/models/folder.js
import DS from 'ember-data';

export default DS.Model.extend({
  parent: DS.belongsTo('folder', { inverse: null })
});
```

<!--
### Polymorphism
-->

### ポリモーフィズム

<!--
Polymorphism is a powerful concept which allows a developer
to abstract common functionality into a base class. Consider the
following example: a user with multiple payment methods. They
could have a linked PayPal account, and a couple credit cards on
file.
-->

ポリモーフィズム(多態性)は、開発者が共通の機能を基本クラスに抽象化することを可能にする強力な概念です。
複数の支払い方法を持つユーザーを例に考えてみましょう。
ユーザーはPayPalアカウントとクレジットカードをいくつか持てるとします。

<!--
Note that, for polymorphism to work, Ember Data expects a
"type" declaration polymorphic type via the reserved `type`
property on the model. Confused? See the API response below.
-->

ポリモーフィズムを機能させるために、Ember Dataでは、モデルの予約語プロパティの`type`でポリモーフィック関連を示します。
混乱してますか？
下記のAPIのレスポンスを見てください。

<!--
First, let's look at the model definitions:
-->

まず、モデル定義を見てみましょう。

```app/models/user.js
import DS from 'ember-data';

export default DS.Model.extend({
  paymentMethods: DS.hasMany('payment-method', { polymorphic: true })
});
```

```app/models/payment-method.js
import DS from 'ember-data';

export default DS.Model.extend({
  user: DS.belongsTo('user', { inverse: 'paymentMethods' }),
});
```

```app/models/payment-method-cc.js
import { computed } from '@ember/object';
import PaymentMethod from './payment-method';

export default PaymentMethod.extend({
  obfuscatedIdentifier: computed('last4', function () {
    return `**** **** **** ${this.get('last4')}`;
  })
});
```

```app/models/payment-method-paypal.js
import { computed } from '@ember/object';
import DS from 'ember-data';
import PaymentMethod from './payment-method'

export default PaymentMethod.extend({
  linkedEmail: DS.attr(),

  obfuscatedIdentifier: computed('linkedEmail', function () {
    let last5 = this.get('linkedEmail').split('').reverse().slice(0, 5).reverse().join('');

    return `••••${last5}`;
  })
});
```

<!--
And our API might setup these relationships like so:
-->

APIではこういった関係を以下のように表します。

```json
{
	"data": {
		"id": "8675309",
		"type": "user",
		"attributes": {
			"name": "Anfanie Farmeo"
		},
		"relationships": {
			"payment-methods": {
				"data": [{
					"id": "1",
					"type": "payment-method-paypal"
				}, {
					"id": "2",
					"type": "payment-method-cc"
				}, {
					"id": "3",
					"type": "payment-method-apple-pay"
				}]
			}
		}
	},
	"included": [{
		"id": "1",
		"type": "payment-method-paypal",
		"attributes": {
			"linked-email": "ryan@gosling.io"
		}
	}, {
		"id": "2",
		"type": "payment-method-cc",
		"attributes": {
			"last4": "1335"
		}
	}, {
		"id": "3",
		"type": "payment-method-apple-pay",
		"attributes": {
			"last4": "5513"
		}
	}]
}
```

<!--
### Readonly Nested Data
-->

### 読み取り専用のネストしたデータ

<!--
Some models may have properties that are deeply nested objects of
readonly data. The naïve solution would be to define models for each
nested object and use `hasMany` and `belongsTo` to recreate the nested
relationship. However, since readonly data will never need to be
updated and saved this often results in the creation of a great deal
of code for very little benefit. An alternate approach is to define
these relationships using an attribute with no transform
(`DS.attr()`). This makes it easy to access readonly values in
computed properties and templates without the overhead of defining
extraneous models.
-->

一部のモデルでは、プロパティに深くネストした読み取り専用のデータオブジェクトを持つ場合があります。
単純な解決策は、各ネストされたオブジェクトのモデルを定義し、`hasMany`と`belongsTo`を使用してネストした関係を再定義することです。
しかし、読み込み専用のデータは更新して保存する必要がないため、ほとんどの場合、大量のコードを書く割に得られるメリットが少ないという結果になります。
他の解決策は、これらの関係を変換のないアトリビュートとして(`DS.attr()`)定義することです。
これにより、余分なモデルの定義にコストをかけずに、算出プロパティとテンプレートで読み取り専用のデータにに簡単にアクセスできるようになります。

<!--
### Creating Records
-->

### レコードの作成

<!--
Let's assume that we have a `blog-post` and a `comment` model, which are related to each other as follows:
-->

`blog-post`と`comment`モデルがあり、次のように相互に関連しているとしましょう。

```app/models/blog-post.js
import DS from 'ember-data';

export default DS.Model.extend({
  comments: DS.hasMany('comment')
});
```

```app/models/comment.js
import DS from 'ember-data';

export default DS.Model.extend({
  blogPost: DS.belongsTo('blog-post')
});
```

<!--
When a user comments on a blogPost, we need to create a relationship between the two records. We can simply set the `belongsTo` relationship in our new comment:
-->

ユーザーがblogPostにコメントする時は、2つのレコードを関連付ける必要があります。
新しいコメントの`belongsTo`関係を設定するだけでできます。

```javascript
let blogPost = this.get('store').peekRecord('blog-post', 1);
let comment = this.get('store').createRecord('comment', {
  blogPost: blogPost
});
comment.save();
```

<!--
This will create a new `comment` record and save it to the server. Ember Data will also update the blogPost to include our newly created comment in its `comments` relationship.
-->

これにより、`comment`レコードが作成され、サーバーに保存されます。
Ember Dataは、新たに作成されたコメントを`comments`関係に含めるためにblogPostも更新します。

<!--
We could have also linked the two records together by updating the blogPost's `hasMany` relationship:
-->

blogPostの`hasMany`関係を更新することで、2つのレコードをリンクすることもできます。

```javascript
let blogPost = this.get('store').peekRecord('blog-post', 1);
let comment = this.get('store').createRecord('comment', {
});
blogPost.get('comments').pushObject(comment);
comment.save().then(function () {
  blogPost.save();
});
```

<!--
In this case the new comment's `belongsTo` relationship will be set to the parent blogPost.
-->

この場合、新しいコメントの`belongsTo`関係は親のblogPostに設定されます。

<!--
Although `createRecord` is fairly straightforward, the only thing to watch out for
is that you cannot assign a promise as a relationship, currently.
-->

`createRecord`を使うのは簡単ですが、Promiseを設定できないことに気をつけてください。

<!--
For example, if you want to set the `author` property of a blogPost, this would **not** work
if the `user` with id isn't already loaded into the store:
-->

例えば、blogPostの`author`プロパティを設定する時に、指定したidを持つ`user`がまだストアに読み込まれていない場合、以下のやり方では動きません。

```js
this.get('store').createRecord('blog-post', {
  title: 'Rails is Omakase',
  body: 'Lorem ipsum',
  author: this.get('store').findRecord('user', 1)
});
```

<!--
However, you can easily set the relationship after the promise has fulfilled:
-->

ですが、Promiseが解決された後なら簡単に関係を設定することができます。


```js
let blogPost = this.get('store').createRecord('blog-post', {
  title: 'Rails is Omakase',
  body: 'Lorem ipsum'
});

this.get('store').findRecord('user', 1).then(function(user) {
  blogPost.set('author', user);
});
```

<!--
### Retrieving Related Records
-->

### 関連データの取得

<!--
When you request data from the server for a model that has relationships with one or more others,
you may want to retrieve records corresponding to those related models at the same time.
For example, when retrieving a blog post, you may need to access the comments associated
with the post as well.
The [JSON API specification allows](http://jsonapi.org/format/#fetching-includes)
servers to accept a query parameter with the key `include` as a request to
include those related records in the response returned to the client.
The value of the parameter should be a comma-separated list of names of the
relationships required.
-->

他のモデルと関係を持つモデルのデータをサーバーに要求する時、関連するモデルのデータも同時に取得したいことがあります。
例えば、ブログ記事を取得時に、記事に関連付けられたコメントも必要な場合、
[JSON API仕様](http://jsonapi.org/format/#fetching-includes)では、サーバに関連したデータをクライアントに返すレスポンスに含めるように要求するために、クエリパラメーターで`include`キーを使います。
パラメーターの値は、必要な関係モデルの名前をコンマ区切って指定します。

<!--
If you are using an adapter that supports JSON API, such as Ember's default [`JSONAPIAdapter`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.JSONAPIAdapter),
you can easily add the `include` parameter to the server requests created by
the `findRecord()`, `findAll()`,
`query()` and `queryRecord()` methods.
-->

Emberのデフォルト[`JSONAPIAdapter`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.JSONAPIAdapter)などのJSON APIをサポートするアダプターを使用している場合は、`findRecord()`、`findAll()`、`query()`および`queryRecord()`メソッドで`include`パラメーターを簡単に追加できます。

<!--
`findRecord()` and `findAll()` each take an `options` argument in which you can
specify the `include` parameter.
For example, given a `post` model that has a `hasMany` relationship with a `comment` model,
when retrieving a specific post we can have the server also return that post's comments
as follows:
-->

`findRecord()`と`findAll()`では`options`引数で`include`パラメータを指定できます。
例えば、`comment`モデルとの`hasMany`関係を持つ`post`モデルがある場合、ある記事を取得する際に、サーバーにその記事のコメントも含めて返すように伝えるには、以下のようにします。

```app/routes/post.js
import Route from '@ember/routing/route';

export default Route.extend({
  model(params) {
   return this.store.findRecord('post', params.post_id, {include: 'comments'});
  }
});
```

<!--
The post's comments would then be available in your template as `model.comments`.
-->

コメントはテンプレート内で`model.comments`として利用できるようになります。

<!--
Nested relationships can be specified in the `include` parameter as a dot-separated sequence of relationship names.
So to request both the post's comments and the authors of those comments the request
would look like this:
-->

ネストした関係は、`include`パラメーターに関連付けの名前をドット区切りで指定します。
記事のコメントとコメントの投稿者の両方を取得するには、以下のように書きます。

```app/routes/post.js
import Route from '@ember/routing/route';

export default Route.extend({
  model(params) {
   return this.store.findRecord('post', params.post_id, {include: 'comments,comments.author'});
  }
});
```

<!--
The `query()` and `queryRecord()` methods each take a `query` argument that is
serialized directly into the URL query string and the `include` parameter may
form part of that argument.
For example:
-->

`query()`と`queryRecord()`メソッドは、URLのクエリーに直接シリアライズされる`query`引数を取ります。
`include`パラメーターはその引数内で指定します。例:

```app/routes/adele.js
import Route from '@ember/routing/route';

export default Route.extend({
  model() {
    // GET to /artists?filter[name]=Adele&include=albums
    this.store.query('artist', {
      filter: {name: 'Adele'},
      include: 'albums'
    }).then(function(artists) {
      return artists.get('firstObject');
    });
  }
});
```

<!--
### Updating Existing Records
-->

### レコードの更新

<!--
Sometimes we want to set relationships on already existing records. We can simply set a `belongsTo` relationship:
-->

既存のレコードの関連付けをしたい場合は、単に`belongsTo`関係を設定するだけです。

```javascript
let blogPost = this.get('store').peekRecord('blog-post', 1);
let comment = this.get('store').peekRecord('comment', 1);
comment.set('blogPost', blogPost);
comment.save();
```

<!--
Alternatively, we could update the `hasMany` relationship by pushing a record into the relationship:
-->

あるいは、関係をレコードにプッシュすることによって`hasMany`関係を更新することができます。

```javascript
let blogPost = this.get('store').peekRecord('blog-post', 1);
let comment = this.get('store').peekRecord('comment', 1);
blogPost.get('comments').pushObject(comment);
blogPost.save();
```

<!--
### Removing Relationships
-->

### 関連の解消

<!--
To remove a `belongsTo` relationship, we can set it to `null`, which will also remove it from the `hasMany` side:
-->

`belongsTo`関係を削除するには、`null`に設定を設定します。
これにより、`hasMany`側との関係も無くなります。

```javascript
let comment = this.get('store').peekRecord('comment', 1);
comment.set('blogPost', null);
comment.save();
```

<!--
It is also possible to remove a record from a `hasMany` relationship:
-->

`hasMany`関係からレコードを削除することもできます。

```javascript
let blogPost = this.get('store').peekRecord('blog-post', 1);
let comment = this.get('store').peekRecord('comment', 1);
blogPost.get('comments').removeObject(comment);
blogPost.save();
```

<!--
As in the earlier examples, the comment's `belongsTo` relationship will also be cleared by Ember Data.
-->

前の例と同様に、コメントの`belongsTo`関係もEmber Dataによってクリアされます。

<!--
### Relationships as Promises
-->

### 関連付けとPromise

<!--
While working with relationships it is important to remember that they return promises.
-->

関連付けられたモデルを扱う時は、Promiseが返されるということを覚えておくことが重要です。

<!--
For example, if we were to work on a blogPost's asynchronous comments, we would have to wait until the promise has fulfilled:
-->

例えば、blogPostのコメントが非同期の場合、Promiseが解決されるまで待つ必要があります。

```javascript
let blogPost = this.get('store').peekRecord('blog-post', 1);

blogPost.get('comments').then((comments) => {
  // now we can work with the comments
});
```

<!--
The same applies to `belongsTo` relationships:
-->

`belongsTo`関係にも同じことが言えます。

```javascript
let comment = this.get('store').peekRecord('comment', 1);

comment.get('blogPost').then((blogPost) => {
  // the blogPost is available here
});
```

<!--
Handlebars templates will automatically be updated to reflect a resolved promise. We can display a list of comments in a blogPost like so:
-->

ハンドルバーテンプレートはPromiseの解決を自動的に反映します。
そのため以下のように書くだけで、blogPostにコメントの一覧を表示することができます。

```handlebars
<ul>
  {{#each blogPost.comments as |comment|}}
    <li>{{comment.id}}</li>
  {{/each}}
</ul>
```

<!--
Ember Data will query the server for the appropriate records and re-render the template once the data is received.
-->

Ember Dataはサーバーに適切なレコードを要求し、データを取得するとテンプレートを再描画します。
