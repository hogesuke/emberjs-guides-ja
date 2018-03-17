
<!--
In Ember Data, an Adapter determines how data is persisted to a
backend data store. Things such as the backend host, URL format
 and headers used to talk to a REST API can all be configured 
 in an adapter. You can even switch to storing data in local storage
 using a [local storage adapter](https://github.com/locks/ember-localstorage-adapter). 
-->

Ember Dataでは、アダプターがデータをバックエンドデータストアに永続化する方法を決定します。
バックエンドホスト、URLのフォーマット、REST APIの送受信に使用されるヘッダーなどは、すべてアダプターで設定できます。
[ローカルストレージアダプター](https://github.com/locks/ember-localstorage-adapter)を使用してローカルストレージにデータを格納するように切り替えることもできます。

<!--
Ember Data's default Adapter has some built-in assumptions about
how a [REST API should look](http://jsonapi.org/). If your backend conventions
differ from those assumptions, Ember Data allows either slight adjustments
or you can switch to a different adapter if your backend works noticeably
differently.
-->

Ember Dataのデフォルトアダプターには、[REST APIはどうあるべきか](http://jsonapi.org/)のいくつかの組み込みの前提があります。
バックエンドの規約がこれらの前提と異なる場合、Ember Dataは少し調整するか、バックエンドが著しく異なる場合に、別のアダプタに切り替えることができます。

<!--
_(If you're looking to adjust how the data sent to the backend is formatted,
check the [serializer](../customizing-serializers/) page.)_
-->

_バックエンドに送信するデータのフォーマットの変更については、[シリアライザー](../customizing-serializers/)のページを確認してください。_

<!--
Extending Adapters is a natural process in Ember Data. Ember takes the
position that you should extend an adapter to add different
functionality. This results in code that is
more testable, easier to understand and reduces bloat for people who
may want to subclass your adapter.
-->

アダプターの拡張は、Ember Dataの自然なプロセスです。
Emberは、さまざまな機能を追加するためにはアダプターを拡張すべきという見解を示しています。
これにより、アダプターをサブクラス化する場合でも、テスト可能で理解しやすいく、コードは肥大化しないでしょう。

<!--
If your backend has some consistent rules you can define an
`adapter:application`. The `adapter:application` will get priority over
the default Adapter, however it will still be superseded by model
specific Adapters.
-->

バックエンドに一定の規則がある場合は、`adapter:application`で定義できます。
`adapter:application`は、デフォルトのアダプターより優先されますが、モデル固有のアダプターによりオーバーライドされます。

```app/adapters/application.js
import DS from 'ember-data';

export default DS.JSONAPIAdapter.extend({
  // Application specific overrides go here
});
```

<!--
If you have one model that has exceptional rules for communicating
with its backend than the others you can create a Model specific
Adapter by running the command `ember generate adapter adapter-name`.
For example, running `ember generate adapter post` will create the
following file:
-->

バックエンドと通信で例外的なルールを持つモデルがある場合は、コマンド`ember generate adapter adapter-name`を実行してモデル固有のアダプターを作成します。
例えば、`ember generate adapter post`を実行すると、次のファイルが作成されます。

```app/adapters/post.js
import DS from 'ember-data';

export default DS.JSONAPIAdapter.extend({
  namespace: 'api/v1'
});
```

<!--
Ember Data comes with several built-in adapters.
Feel free to use these adapters as a starting point for creating your own custom adapter.
-->

Ember Dataには、いくつかのビルトインアダプターが付属しています。
独自のカスタムアダプターを作成するための出発点として、これらのアダプターを自由に使用してください。

<!--
- [DS.Adapter](https://www.emberjs.com/api/ember-data/release/classes/DS.Adapter) is the basic adapter
with no functionality. It is generally a good starting point if you
want to create an adapter that is radically different from the other
Ember adapters.

- [DS.JSONAPIAdapter](https://www.emberjs.com/api/ember-data/release/classes/DS.JSONAPIAdapter)
The `JSONAPIAdapter` is the default adapter and follows JSON API
conventions to communicate with an HTTP server by transmitting JSON
via XHR.

- [DS.RESTAdapter](https://www.emberjs.com/api/ember-data/release/classes/DS.RESTAdapter)
The `RESTAdapter` allows your store to communicate with an HTTP server
by transmitting JSON via XHR. Before Ember Data 2.0 this adapter was the default.
-->

- [DS.Adapter](https://www.emberjs.com/api/ember-data/release/classes/DS.Adapter)は機能を持たない基本アダプターです。
他のEmberアダプターとは根本的に異なるアダプターを作成する場合は、一般的には適切な出発点です。
- [DS.JSONAPIAdapter](https://www.emberjs.com/api/ember-data/release/classes/DS.JSONAPIAdapter) `JSONAPIAdapter`はデフォルトのアダプターであり、XHR経由でJSONを送信してHTTPサーバーと通信するためのJSON API規約に従います。
- [DS.RESTAdapter](https://www.emberjs.com/api/ember-data/release/classes/DS.RESTAdapter) `RESTAdapter`を使用すると、JSONをXHR経由で送信し、ストアがHTTPサーバーと通信できるようになります。 Ember Data 2.0以前はこのアダプターがデフォルトでした。


<!--
## Customizing the JSONAPIAdapter
-->

##  JSONAPIAdapterのカスタマイズ

<!--
The
[DS.JSONAPIAdapter](https://www.emberjs.com/api/ember-data/release/classes/DS.JSONAPIAdapter)
has a handful of hooks that are commonly used to extend it to work
with non-standard backends.
-->

[DS.JSONAPIAdapter](https://www.emberjs.com/api/ember-data/release/classes/DS.JSONAPIAdapter)には、便利なフックがあり、非標準のバックエンドと連携できるように拡張するために使用できます。

<!--
### URL Conventions
-->

### URLの規約

<!--
The `JSONAPIAdapter` is smart enough to determine the URLs it
communicates with based on the name of the model. For example, if you
ask for a `Post` by ID:
-->

`JSONAPIAdapter`はスマートで、モデル名に基づいて通信するURLを決定します。
例えば、IDで`Post`を要求する場合は、次のようにします。

```js
store.findRecord('post', 1).then(function(post) {
});
```

<!--
The JSON API adapter will automatically send a `GET` request to `/posts/1`.
-->

JSON APIアダプターは自動的に`/posts/1`に`GET`リクエストを送ります。


<!--
The actions you can take on a record map onto the following URLs in the
JSON API adapter:
-->

レコード上で使用できるアクションは、JSONAPIアダプターで以下のURLにマップされます。

<!--
<table>
  <thead>
    <tr><th>Action</th><th>HTTP Verb</th><th>URL</th></tr>
  </thead>
  <tbody>
    <tr><th>Find</th><td>GET</td><td>/posts/123</td></tr>
    <tr><th>Find All</th><td>GET</td><td>/posts</td></tr>
    <tr><th>Update</th><td>PATCH</td><td>/posts/123</td></tr>
    <tr><th>Create</th><td>POST</td><td>/posts</td></tr>
    <tr><th>Delete</th><td>DELETE</td><td>/posts/123</td></tr>
  </tbody>
</table>
-->

<table>
  <thead>
    <tr><th>アクション</th><th>HTTP メソッド</th><th>URL</th></tr>
  </thead>
  <tbody>
    <tr><th>Find</th><td>GET</td><td>/posts/123</td></tr>
    <tr><th>Find All</th><td>GET</td><td>/posts</td></tr>
    <tr><th>Update</th><td>PATCH</td><td>/posts/123</td></tr>
    <tr><th>Create</th><td>POST</td><td>/posts</td></tr>
    <tr><th>Delete</th><td>DELETE</td><td>/posts/123</td></tr>
  </tbody>
</table>

<!--
#### Pluralization Customization
--->

#### 複数形化のカスタマイズ

<!--
To facilitate pluralizing model names when generating route urls Ember
Data comes bundled with
[Ember Inflector](https://github.com/stefanpenner/ember-inflector), an
ActiveSupport::Inflector compatible library for inflecting words
between plural and singular forms. Irregular or uncountable
pluralizations can be specified via `Ember.Inflector.inflector`.
A common way to do this is:
-->

Ember Dataでは、URLを生成する際に複数形のモデル名が使用され、複数形式と単数形式の間で単語を使用するためのActiveSupport::Inflector互換ライブラリ[Ember Inflector](https://github.com/stefanpenner/ember-inflector)がバンドルされています。
`Ember.Inflector.inflector`を使用して、不規則なものや不可算名詞の複数化を指定できます。
これを行う一般的な方法は次のとおりです。

```app/app.js
// sets up Ember.Inflector
import './models/custom-inflector-rules';
```

```app/models/custom-inflector-rules.js
import Inflector from 'ember-inflector';

const inflector = Inflector.inflector;

inflector.irregular('formula', 'formulae');
inflector.uncountable('advice');

// Meet Ember Inspector's expectation of an export
export default {};
```

<!--
This will tell the JSON API adapter that requests for `formula`
should go to `/formulae/1` instead of `/formulas/1`, and that
requests for `advice` should go to `/advice/1` instead of `/advices/1`.
-->

これはJSON APIアダプターに、`formoula`のリクエストが`/formulas/1`ではなく`/formulae/1`に行き、`advice`のリクエストが`/advices/1`の代わりに`/advice/`1に行かなければならないことを伝えます。

<!--
When specifying irregular inflection rules for compound words, only the final word or phrase should be specified. For example, to specify the plural of `redCow` as `redKine` or `red-cow` as `red-kine`, only the final word segments `cow` and `kine` should be specified:
-->

複合語に不規則な語形変化を指定する場合は、最後の単語または句のみを指定する必要があります。
例えば、`redCow` `red-cow`の複数形を`redKine` `red-cow`と指定するには、最終の単語の`cow`と`kine`のみを指定する必要があります。

```js
inflector.irregular('cow', 'kine');
```

<!--
#### Endpoint Path Customization
-->

#### エンドポイントパスのカスタマイズ

<!--
The `namespace` property can be used to prefix requests with a
specific url namespace.
-->

`namespace`プロパティを使用して、要求に特定のurl名前空間のプレフィックスを付けることができます。

```app/adapters/application.js
import DS from 'ember-data';

export default DS.JSONAPIAdapter.extend({
  namespace: 'api/1'
});
```

<!--
Requests for `person` would now target `http://emberjs.com/api/1/people/1`.
-->

`person`のリクエストは、`http://emberjs.com/api/1/people/1`を対象とするようになります。

<!--
#### Host Customization
-->

#### ホストのカスタマイズ

<!--
By default, the adapter will target the current domain. If you would
like to specify a new domain you can do so by setting the `host`
property on the adapter.
-->

デフォルトでは、アダプターは現在のドメインをターゲットにします。
他のドメインを指定する場合は、アダプターに`host`プロパティーを設定することで行うことができます。

```app/adapters/application.js
import DS from 'ember-data';

export default DS.JSONAPIAdapter.extend({
  host: 'https://api.example.com'
});
```

<!--
Requests for `person` would now target `https://api.example.com/people/1`.
-->

`person`のリクエストは、`https://api.example.com/people/1`を対象とするようになります。

<!--
#### Path Customization
-->

#### パスのカスタマイズ

<!--
By default, the `JSONAPIAdapter` will attempt to pluralize and dasherize
the model name to generate the path name. If this convention does not
conform to your backend you can override the `pathForType` method.
-->

デフォルトでは、`JSONAPIAdapter`はパス名を生成する際にモデル名を複数形にしてスネークケースに変換しようとします。
この規約がバックエンドに準拠していない場合は、`pathForType`メソッドをオーバーライドします。

<!--
For example, if you did not want to pluralize model names and needed
underscore_case instead of camelCase you could override the
`pathForType` method like this:
-->

例えば、モデル名を複数形にする必要がなく、キャメルケースではなくスネークケースが必要な場合は、次のように`pathForType`メソッドをオーバーライドします。

```app/adapters/application.js
import DS from 'ember-data';
import { underscore } from '@ember/string';

export default DS.JSONAPIAdapter.extend({
  pathForType(type) {
    return underscore(type);
  }
});
```

<!--
Requests for `person` would now target `/person/1`.
Requests for `user-profile` would now target `/user_profile/1`.
-->

`person`のリクエストは、`/person/1`を対象とするようになります。
`user-profile`のリクエストは、`/user_profile/1`を対象とするようになります。

#### Headers customization

<!--
Some APIs require HTTP headers, e.g. to provide an API key. Arbitrary
headers can be set as key/value pairs on the `JSONAPIAdapter`'s `headers`
object and Ember Data will send them along with each ajax request.
-->

一部のAPIでは、HTTPヘッダーが必要です。
APIキーを提供する場合、`JSONAPIAdapter`の`headers`オブジェクトに任意のヘッダをキーと値のペアとして設定することができ、Ember Dataはajaxする際にそれらを送信することができます。

```app/adapters/application.js
import DS from 'ember-data';

export default DS.JSONAPIAdapter.extend({
  headers: {
    'API_KEY': 'secret key',
    'ANOTHER_HEADER': 'Some header value'
  }
});
```

<!--
`headers` can also be used as a computed property to support dynamic
headers. In the example below, the headers are generated with a computed
property dependent on the `session` service.
-->

動的ヘッダーをサポートするために、ヘッダーを算出プロパティとして使用することもできます。
以下の例では、ヘッダーは`session`サービスに依存する算出プロパティで生成されます。

```app/adapters/application.js
import DS from 'ember-data';
import { computed } from '@ember/object';
import { inject as service } from '@ember/service';


export default DS.JSONAPIAdapter.extend({
  session: service('session'),
  headers: computed('session.authToken', function() {
    return {
      'API_KEY': this.get('session.authToken'),
      'ANOTHER_HEADER': 'Some header value'
    };
  })
});
```

<!--
In some cases, your dynamic headers may require data from some
object outside of Ember's observer system (for example
`document.cookie`). You can use the
[volatile](https://www.emberjs.com/api/ember/2.16/classes/@ember%2Fobject%2Fcomputed/methods/property?anchor=volatile&show=inherited%2Cprotected%2Cprivate%2Cdeprecated)
function to set the property into a non-cached mode causing the headers to
be recomputed with every request.
-->

場合によっては、動的ヘッダーにEmberのオブザーバーシステム外のオブジェクト(例えば`document.cookie`)のデータが必要な場合があります。
[volatile](https://www.emberjs.com/api/ember/2.16/classes/@ember%2Fobject%2Fcomputed/methods/property?anchor=volatile&show=inherited%2Cprotected%2Cprivate%2Cdeprecated)関数を使用して、キャッシュされていないモードにプロパティを設定して、すべてのリクエストでヘッダーを再計算することができます。

```app/adapters/application.js
import DS from 'ember-data';
import { computed } from '@ember/object';
import { get } from '@ember/object';

export default DS.JSONAPIAdapter.extend({
  headers: computed(function() {
    return {
      'API_KEY': get(document.cookie.match(/apiKey\=([^;]*)/), '1'),
      'ANOTHER_HEADER': 'Some header value'
    };
  }).volatile()
});
```

#### Authoring Adapters

<!--
The `defaultSerializer` property can be used to specify the serializer
that will be used by this adapter. This is only used when a model
specific serializer or `serializer:application` are not defined.
-->

`defaultSerializer`プロパティを使用して、このアダプターで使用されるシリアライザを指定できます。
これは、モデル固有のシリアライザまたは`serializer:application`が定義されていない場合にのみ使用されます。

<!--
In an application, it is often easier to specify an
`serializer:application`. However, if you are the author of a
community adapter it is important to remember to set this property to
ensure Ember does the right thing in the case a user of your adapter
does not specify an `serializer:application`.
-->

アプリケーションでは、`serializer:application`を指定するほうが簡単です。
ただし、コミュニティアダプターの作成する場合は、ユーザーが`serializer:application`を指定していない場合に、Emberが正しいことを行うように、このプロパティを設定することを忘れないようにしてください。

```app/adapters/my-custom-adapter.js
import DS from 'ember-data';

export default DS.JSONAPIAdapter.extend({
  defaultSerializer: '-default'
});
```

<!--
## Community Adapters
-->

## コミュニティアダプター

<!--
If none of the built-in Ember Data Adapters work for your backend,
be sure to check out some of the community maintained Ember Data
Adapters. Some good places to look for Ember Data Adapters include:
-->

組み込みのEmber DATAのアダプターのどれもバックエンドで動作しない場合は
コミュニティがメンテナンスしているのEmber DATAのアダプターをチェックしてください。
Ember Dataのアダプターを探すには、次のような場所があります。

- [Ember Observer](http://emberobserver.com/categories/data)
- [GitHub](https://github.com/search?q=ember+data+adapter&ref=cmdform)
