<!--
Models are objects that represent the underlying data that your
application presents to the user. Different apps will have very
different models, depending on what problems they're trying to solve.
-->

モデルは、ユーザーに見せるデータの基礎を表すオブジェクトです。
解決しようとしている問題に応じて、アプリ毎に様々なモデルが存在します。

<!--
For example, a photo sharing application might have a `Photo`
model to represent a particular photo, and a `PhotoAlbum` that
represents a group of photos. In contrast, an online shopping app would
probably have different models, like `ShoppingCart`, `Invoice`, or
`LineItem`.
-->

例えば、写真共有アプリには、特定の写真を表す`Photo`モデルと、写真集を表す`PhotoAlbum`があったり、
オンラインショッピングアプリには、`ShoppingCart`(カート)、`Invoice`(請求書)、`LineItem`(カートに追加された商品)などの違ったモデルがあったりするでしょう。

<!--
Models tend to be _persistent_. That means the user does not expect
model data to be lost when they close their browser window. To make sure
no data is lost, if the user makes changes to a model, you need to store
the model data somewhere that it will not be lost.
-->

モデルは永続化することが多いです。
永続化により、ユーザーがブラウザを閉じるのと同時にモデルデータを失うのを防ぐことができます。
データが失われないようにするには、ユーザーがモデルを変更した時に、そのデータを失われない場所に保存する必要があります。

<!--
Typically, most models are loaded from and saved to a server that uses a
database to store data. Usually you will send JSON representations of
models back and forth to an HTTP server that you have written. However,
Ember makes it easy to use other durable storage, such as saving to the
user's hard disk with [IndexedDB][indexeddb], or hosted storage solutions that let you
avoid writing and hosting your own servers.
-->

一般的に、ほとんどのモデルはサーバからデータを取得し、サーバのデータベースに保存します。
HTTPサーバーとのモデルデータの送受信には、JSON形式がよく使われます。
ですが、Emberでは、[IndexedDB][indexeddb]を使用してユーザーのハードディスクに保存したり、他の耐久性のあるストレージや、独自のサーバーを作成しホストすることを避けることができるホスト型ストレージソリューションを簡単に使用できます。

[indexeddb]: https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API

<!--
Once you've loaded your models from storage, components know how to
translate model data into a UI that your user can interact with.  For
more information about how components get model data, see the
[Specifying a Route's Model](../routing/specifying-a-routes-model)
guide.
-->

モデルがストレージからロードされると、コンポーネントがモデルデータをユーザーが操作できるUIに変換します。
コンポーネントにモデルデータを渡す方法の詳細は、[ルートのモデルを指定する](../routing/specifying-a-routes-model)を参照してください。

<!--
Ember Data, included by default when you create a new application, is a
library that integrates tightly with Ember to make it easy to retrieve
models from your server as JSON, save updates back to the server, and
create new models in the browser.
-->

Ember DATAは、新しくアプリケーションを作成する際にデフォルトで含まれます。
Ember DATAはEmberと緊密に統合されたライブラリで、サーバーからモデルをJSON形式で取得したり、サーバーにデータの変更を保存したり、ブラウザ上でデータを作成するのを簡単にします。

<!--
Thanks to its use of the _adapter pattern_, Ember Data can be configured
to work with many different kinds of backends. There is [an entire
ecosystem of adapters][adapters] that allow your Ember app to talk to different
types of servers without you writing any networking code.
-->

アダプタパターンが使われているお陰で、いろんな種類のバックエンドでEmber Dataを使えるよう変更できます。
さまざまな種類のサーバーとのやり取りを可能にする[アダプターがよく揃っているエコシステム][adapters]があるため、ライブラリを使えば自分でコードを書かなくて済むでしょう。

[adapters]: http://emberobserver.com/categories/ember-data-adapters

<!--
If you need to integrate your Ember.js app with a server that does not
have an adapter available (for example, you hand-rolled an API server
that does not adhere to any JSON specification), Ember Data is designed
to be configurable to work with whatever data your server returns.
-->

対応したアダプターがなくても(JSON仕様に準拠していない独自仕様のAPIサーバーなど)、Ember Dataは、サーバがどんな形式で返そうが、設定によって使えるように設計されています。

<!--
Ember Data is also designed to work with streaming servers, like those
powered by WebSockets. You can open a socket to your server and push
changes into Ember Data whenever they occur, giving your app a real-time
user interface that is always up-to-date.
-->

Ember Dataは、WebSocketのようなストリーミングサーバーとも使えるよう設計されています。
サーバーへのソケットを開き、変更が発生するたびにEmber Dataに変更をプッシュして、常に最新のリアルタイムのユーザーインターフェイスをアプリ上で提供することができます。

<!--
At first, using Ember Data may feel different than the way you're used
to writing JavaScript applications. Many developers are familiar with
using AJAX to fetch raw JSON data from an endpoint, which may appear
easy at first. Over time, however, complexity leaks out into your
application code, making it hard to maintain.
-->

初めてEmber Dataを使う時に、それまでのJavaScriptアプリケーションの作成方法との違和感を感じるかもしれません。
よく、エンドポイントから生のJSONデータをAJAXで取得していた場合、一見その方が簡単そうですが、
時間の経過とともに、アプリケーションコードが複雑になると、そのやり方では保守が困難になるでしょう。

<!--
With Ember Data, managing models as your application grows becomes both
simple _and_ easy.
-->

Ember Dataを使用すると、アプリケーションとしてのモデルの管理が容易になります。

<!--
Once you have an understanding of Ember Data, you will have a much
better way to manage the complexity of data loading in your application.
This will allow your code to evolve without becoming a mess.
-->

Ember Dataを理解すると、アプリケーションでのデータの読み込みの複雑さをうまく管理するより良い方法を得られます。
それにより、コードを汚くすることなく書いていくことができるでしょう。

<!--
## The Store and a Single Source of Truth
-->

## Storeと信頼できる唯一の情報源

*訳注: a Single Source of Truthを信頼できる唯一の情報源と訳しています。*

<!--
One common way of building web applications is to tightly couple user
interface elements to data fetching. For example, imagine you are
writing the admin section of a blogging app, which has a feature that
lists the drafts for the currently logged in user.
-->

Webアプリ開発でよくあるやり方の1つに、ユーザーインターフェイス要素とデータ取得を密結合させるのがあります。
例えば、ブログアプリの管理画面を開発していて、ログイン中のユーザーの下書き一覧ページを作る場合...

<!--
You might be tempted to make the component responsible for fetching that
data and storing it:
-->

以下のように、データをコンポーネントで取得するように書く気になるかもしれません。

```app/components/list-of-drafts.js
import Component from '@ember/component';

export default Component.extend({
  willRender() {
    $.getJSON('/drafts').then(data => {
      this.set('drafts', data);
    });
  }
});
```

<!--
You could then show the list of drafts in your component's template like
this:
-->

そして以下のように、コンポーネントのテンプレートで下書きの一覧を表示しようとします。

```app/templates/components/list-of-drafts.hbs
<ul>
  {{#each drafts key="id" as |draft|}}
    <li>{{draft.title}}</li>
  {{/each}}
</ul>
```

<!--
This works great for the `list-of-drafts` component. However, your app
is likely made up of many different components. On another page you
may want a component to display the number of drafts. You may be
tempted to copy and paste your existing `willRender` code into the new
component.
-->

このやり方は下書き一覧のコンポーネントには最適です。
しかし、アプリに他にも色々なコンポーネントがある場合はどうでしょう。
他のページでは、下書きの数を表示するコンポーネントが必要かもしれません。
その場合、上記の`willRender`コードをそのコンポーネントにコピペしたくなるかもしれません。

```app/components/drafts-button.js
import Component from '@ember/component';

export default Component.extend({
  willRender() {
    $.getJSON('/drafts').then(data => {
      this.set('drafts', data);
    });
  }
});
```

```app/templates/components/drafts-button.hbs
{{#link-to "drafts" tagName="button"}}
  Drafts ({{drafts.length}})
{{/link-to}}
```

<!--
Unfortunately, the app will now make two separate requests for the
same information. Not only is the redundant data fetching costly in
terms of wasted bandwidth and affecting the perceived speed of your
app, it's easy for the two values to get out-of-sync. You yourself
have probably used a web application where the list of items gets out
of sync with the counter in a toolbar, leading to a frustrating and
inconsistent experience.
-->

残念ながら、この例では、アプリは同じ情報に対して2つのリクエストが別々に発生します。
不必要なリクエストは、無駄な帯域幅やアプリのスピードに影響を及ぼすだけでなく、簡単に同期ずれを起こすでしょう。
おそらくあなた自身も、一覧の件数とがメニューバーの件数と同期しなくなってイライラするようなWebアプリをこれまでに使ったことがあるのではないでしょうか。

<!--
There is also a _tight coupling_ between your application's UI and the
network code. If the url or the format of the JSON payload changes, it
is likely to break all of your UI components in ways that are hard to
track down.
-->

また、アプリケーションのUIとネットワークコードが密結合になっています。
URLやJSONの形式が変更された場合、UIコンポーネントが動かなくなり、しかも修正が必要なコンポーネントの把握に支障を来す可能性があります。

<!--
The SOLID principles of good design tell us that objects should have a
single responsibility. The responsibility of a component should be
presenting model data to the user, not fetching the model.
-->

良い設計のために、SOLID原則はオブジェクトは単一の責任を持つべきだと教えています。
コンポーネントの責任は、モデルをフェッチするのではなく、モデルデータをユーザーに提示することです。

<!--
Good Ember apps take a different approach. Ember Data gives you a single
**store** that is the central repository of models in your application.
Routes and their corresponding controllers can ask the store for models, and the store is
responsible for knowing how to fetch them.
-->

良く設計されたEmberアプリでは別の手段を取ります。
Ember Dataは、アプリケーションのモデルの中央リポジトリである単一の**store**(ストア)を提供します。
コントローラーとルートはストアにモデルの問い合わせができます。
ストアの責任はモデルをフェッチする方法を知っていることです。

<!--
It also means that the store can detect that two different components
are asking for the same model, allowing your app to only fetch the data
from the server once. You can think of the store as a read-through cache
for your app's models. Both routes and their corresponding controllers have access to
this shared store; when they need to display or modify a model, they
first ask the store for it.
-->

また、2つの異なるコンポーネントが同じモデルを要求していることをストアが把握できるため、アプリはサーバーからデータの取得を一度に済ませられます。
ストアは、モデルのリードスルーキャッシュと考えることもできます。
コントローラーとルートがストアにアクセスできます。
モデルを表示したり変更したりする必要があるときは、まずはストアを使いましょう。

<!--
## Convention Over Configuration with JSON API
-->

## JSON APIとCoC(設定より規約)

<!--
You can significantly reduce the amount of code you need to write and
maintain by relying on Ember's conventions. Since these conventions
will be shared among developers on your team, following them leads
to code that is easier to maintain and understand.
-->

Emberの規約に頼ることで、コードの記述量と保守を大幅に減らすことができます。
規約はチームの開発者間で共有されるため、それによりコードの保守と理解をしやすくなります。

<!--
Rather than create an arbitrary set of conventions, Ember Data is
designed to work out of the box with [JSON API][json-api]. JSON API is a
formal specification for building conventional, robust, and performant
APIs that allow clients and servers to communicate model data.
-->

Ember Dataは、一連の規約を作るよりも、[JSON API][json-api]ですぐに動作するように設計されています。
JSON APIは、クライアントとサーバーがモデルデータを通信できるようにする、従来の堅牢で実績のあるAPIを構築するための正式な仕様です。

[json-api]: http://jsonapi.org

<!--
JSON API standardizes how JavaScript applications talk to servers, so
you decrease the coupling between your frontend and backend, and have
more freedom to change pieces of your stack.
-->

JSON APIは、JavaScriptアプリケーションとサーバーの対話方法を標準化しているため、
フロントエンドとバックエンドの結合度を減らし、技術スタックをより自由に変更できます。

<!--
As an analogy, JSON API is to JavaScript apps and API servers what SQL is
to server-side frameworks and databases. Popular frameworks like Ruby on
Rails, Laravel, Django, Spring and more work out of the box with many
different databases, like MySQL, PostgreSQL, SQL Server, and more.
-->

SQLの「サーバー側のフレームワークとデータベース」の関係と同様に、JSON APIは「JavaScriptアプリケーションとAPIサーバー」の関係です。
Ruby on Rails、Laravel、Django、Springなどの人気のあるフレームワークは、MySQL、PostgreSQL、SQL Serverなど、さまざまなデータベースをすぐに使うことができます。

<!--
Frameworks (or apps built on those frameworks) don't need to write
lots of custom code to add support for a new database; as long as that
database supports SQL, adding support for it is relatively easy.
-->

フレームワーク(とフレームワーク上に構築されたアプリケーション)を使う場合、データベースをサポートするためのコードをたくさん書く必要はありません。
そのデータベースがSQLをサポートしていれば、比較的簡単に使えます。

<!--
So too with JSON API. By using JSON API to interop between your Ember
app and your server, you can entirely change your backend stack without
breaking your frontend. And as you add apps for other platforms, such as
iOS and Android, you will be able to leverage JSON API libraries for
those platforms to easily consume the same API your Ember app uses.
-->

JSON APIもそうです。
JSON APIをEmberアプリケーションとサーバーで使用して相互運用することで、フロントエンドを壊すことなくバックエンドスタックを完全に変更できます。
また、iOSやAndroidなどの他のプラットフォーム用のアプリケーションも開発する時は、プラットフォーム用のJSON APIライブラリを活用することで、Emberアプリケーションが使用するAPIと同じAPIを簡単に使用できます。

<!--
## Models
-->

## モデル

<!--
In Ember Data, each model is represented by a subclass of `Model` that
defines the attributes, relationships, and behavior of the data that you
present to the user.
-->

Ember Dataでは、各モデルは`Model`のサブクラスとして定義します。
モデルには、ユーザーに提示するデータの属性や関係や動作を定義します。

<!--
Models define the type of data that will be provided by your server. For
example, a `Person` model might have a `firstName` attribute that is a
string, and a `birthday` attribute that is a date:
-->

モデルの属性には、サーバーから取得するデータの型を定義します。
例えば、`Person`モデルに、文字列の`firstName`属性と、日付の`birthday`属性がある場合、以下のように定義します。

```app/models/person.js
import DS from 'ember-data';

export default DS.Model.extend({
  firstName: DS.attr('string'),
  birthday:  DS.attr('date')
});
```

<!--
A model also describes its relationships with other objects. For
example, an `order` may have many `line-items`, and a
`line-item` may belong to a particular `order`.
-->

モデルは、他のモデルとの関係も示します。
例えば、`order`は多くの`line-items`を持ち、`line-item`は特定の`order`に属する場合は、以下のように定義します。

```app/models/order.js
import DS from 'ember-data';

export default DS.Model.extend({
  lineItems: DS.hasMany('line-item')
});
```

```app/models/line-item.js
import DS from 'ember-data';

export default DS.Model.extend({
  order: DS.belongsTo('order')
});
```

<!--
Models don't have any data themselves, they define the attributes,
relationships and behavior of specific instances, which are called
**records**.
-->

モデルはデータそのものを持たず、レコードと呼ばれる特定のインスタンスの属性、関係、動作を定義します。

<!--
## Records
-->

## レコード

<!--
A **record** is an instance of a model that contains data loaded from a
server. Your application can also create new records and save them back
to the server.
-->

レコードはモデルのインスタンスで、サーバーからロードされたデータを持ちます。
アプリ側でレコードを作成してサーバーに保存することもできます。

<!--
A record is uniquely identified by its model **type** and **ID**.
-->

レコードは、そのモデルの**タイプ**と**ID**によって一意に識別されます。

<!--
For example, if you were writing a contact management app, you might
have a `Person` model. An individual record in your app might
have a type of `person` and an ID of `1` or `steve-buscemi`.
-->

例えば、連絡先管理アプリケーションを作成していて、`Person`モデルがある場合、
個々のレコードは、タイプに`person`と、IDに`1`か`steve-buscemi`を持つ、といった感じです。

```js
this.get('store').findRecord('person', 1); // => { id: 1, name: 'steve-buscemi' }
```

<!--
An ID is usually assigned to a record by the server when you save it for
the first time, but you can also generate IDs client-side.
-->

IDは通常、初めてレコードを保存するときにサーバーによってレコードに割り当てられますが、IDをクライアント側で生成することもできます。

<!--
## Adapter
-->

## アダプター

<!--
An **adapter** is an object that translates requests from Ember (such as
"find the user with an ID of 1") into requests to a server.
-->

アダプターは、Emberからの要求(「IDが1のユーザーを見つける」など)をサーバーへのリクエストに変換するオブジェクトです。

<!--
For example, if your application asks for a `Person` with an ID of
`1`, how should Ember load it? Over HTTP or a WebSocket?  If
it's HTTP, is the URL `/person/1` or `/resources/people/1`?
-->

例えば、アプリケーションがIDが`1`の`Person`を要求した場合、
HTTPまたはWebSocket経由なのか、HTTPの場合はURLは`/person/1`または`/resources/people/1`なのかなど、
Emberはそれをどのようにロードするかの判断が必要です。

<!--
The adapter is responsible for answering all of these questions.
Whenever your app asks the store for a record that it doesn't have
cached, it will ask the adapter for it. If you change a record and save
it, the store will hand the record to the adapter to send the
appropriate data to your server and confirm that the save was
successful.
-->

アダプターはそれらの問いにすべて答える責任があります。
キャッシュされていないレコードをストアに要求すると、アダプターにその要求がいきます。
レコードを変更して保存すると、ストアはレコードをアダプタに渡して、適切なデータをサーバーに送信し、保存が成功したことを確認します。

<!--
Adapters let you completely change how your API is implemented without
impacting your Ember application code.
-->

アダプターを使用すると、Emberアプリケーションコードに影響を与えずにAPIの実装方法を完全に変更できます。

<!--
## Caching
-->

## キャッシュ

<!--
The store will automatically cache records for you. If a record had already
been loaded, asking for it a second time will always return the same
object instance. This minimizes the number of round-trips to the
server, and allows your application to render its UI to the user as fast as
possible.
-->

ストアは自動的にレコードをキャッシュします。
レコードがすでにロードされている場合、2回目以降の要求には、常に同じオブジェクトインスタンスが返されます。
これにより、サーバーへのラウンドトリップ回数が最小限に抑えられ、UIをユーザーにできるだけ速く表示できます。

<!--
For example, the first time your application asks the store for a
`person` record with an ID of `1`, it will fetch that information from
your server.
-->

例えば、IDが`1`の`person`レコードをストアに初めて要求すると、サーバーからその情報がフェッチされます。

<!--
However, the next time your app asks for a `person` with ID `1`, the
store will notice that it had already retrieved and cached that
information from the server. Instead of sending another request for the
same information, it will give your application the same record it had
provided it the first time.  This feature—always returning the same
record object, no matter how many times you look it up—is sometimes
called an _identity map_.
-->

しかし、IDが`1`の`person`を再度要求すると、ストアはその情報はすでにサーバから取得されキャッシュされていることに気づき、
同じ情報に対して新たにリクエストをサーバに送信するのではなく、最初に提供したのと同じレコードを返します。
何度見ても常に同じレコードオブジェクトを返すこの機能は、アイデンティティマップと呼ばれることがあります。

<!--
Using an identity map is important because it ensures that changes you
make in one part of your UI are propagated to other parts of the UI. It
also means that you don't have to manually keep records in sync—you can
ask for a record by ID and not have to worry about whether other parts
of your application have already asked for and loaded it.
-->

アイデンティティマップを使用することは、UIの一部で行った変更をUIの他の部分に確実に反映するのために重要です。
また、手動でレコードを同期させる必要がないことも意味します。
アプリケーション内の他のところでロードされたかどうかを心配する必要なく、IDでレコードを要求できます。

<!--
One downside to returning a cached record is you may find the state of
the data has changed since it was first loaded into the store's
identity map. In order to prevent this stale data from being a problem
for long, Ember Data will automatically make a request in the
background each time a cached record is returned from the store. When
the new data comes in, the record is updated, and if there have been
changes to the record since the initial render, the template is
re-rendered with the new information.
-->

キャッシュされたレコードを返すことの1つの欠点は、データの状態が最初にストアのアイデンティティマップにロードされてから同期されないことです。
この古くなったデータが長い時間問題にならないようにするために、Ember Dataはキャッシュしたレコードをストアから返す度に、自動的にリクエストをバックグラウンドで行います。
新しいデータが取得されると、レコードが更新され、最初のレンダリング以降にレコードが変更された場合、テンプレートは新しい情報で再レンダリングされます。

<!--
## Architecture Overview
-->

## アーキテクチャの概要

<!--
The first time your application asks the store for a record, the store
sees that it doesn't have a local copy and requests it from your
adapter. Your adapter will go and retrieve the record from your
persistence layer; typically, this will be a JSON representation of the
record served from an HTTP server.
-->

ストアにレコードを初めて要求すると、ストアはローカルコピーを持っていないため、アダプターに要求します。
アダプターは永続性レイヤーからレコードを取得します。 
一般的に、永続性レイヤーはHTTPサーバーでレコードをJSONとして表します。

![Diagram showing process for finding an unloaded record](../images/guides/models/finding-unloaded-record-step1-diagram.png)

<!--
As illustrated in the diagram above, the adapter cannot always return the
requested record immediately. In this case, the adapter must make an
_asynchronous_ request to the server, and only when that request finishes
loading can the record be created with its backing data.
-->

上の図に示すように、アダプターは要求されたレコードをいつもすぐに返すことはできません。
この場合、アダプターはサーバーに対して非同期要求を行わなければならず、その要求のロードが完了した時にのみ、データを元にレコードを作成することができます。

<!--
Because of this asynchronicity, the store immediately returns a
_promise_ from the `findRecord()` method. Similarly, any requests that the
store makes to the adapter also return promises.
-->

非同期であるため、ストアの`findRecord()`メソッドはPromiseをすぐに返します。
同様に、ストアがアダプターに対して行う要求も、Promiseを返します。

<!--
Once the request to the server returns with a JSON payload for the
requested record, the adapter resolves the promise it returned to the
store with the JSON.
-->

サーバーへの要求に、要求したレコードのJSONペイロードが返されると、アダプターはストアに返したPromiseを解決してJSONを渡します。

<!--
The store then takes that JSON, initializes the record with the
JSON data, and resolves the promise returned to your application
with the newly-loaded record.
-->

その後、ストアはアダプターから渡されたSONデータでレコードを初期化し、アプリケーションに返したPromiseに解決して、新しくロードされたレコードを渡します。

![Diagram showing process for finding an unloaded record after the payload has returned from the server](../images/guides/models/finding-unloaded-record-step2-diagram.png)

<!--
Let's look at what happens if you request a record that the store
already has in its cache.
-->

ストアにキャッシュにあるレコードをリクエストするとどうなるかを見てみましょう。

![Diagram showing process for finding an unloaded record after the payload has returned from the server](../images/guides/models/finding-loaded-record-diagram.png)

<!--
In this case, because the store already knew about the record, it
returns a promise that it resolves with the record immediately. It does
not need to ask the adapter (and, therefore, the server) for a copy
since it already has it saved locally.
-->

この場合、ストアが既にそのレコードについて知っていたので、Promiseを返し、そのPromiseはすぐにそのレコードで解決されます。
アダプター(サーバー)にデータを求める必要はありません
すでにローカルに保存されているためです。

---

<!--
Models, records, adapters and the store are the core concepts you
should understand to get the most out of Ember Data. The following
sections go into more depth about each of these concepts, and how to
use them together.
-->

モデル、レコード、アダプター、ストアは、Ember Dataを最大限に活用するために理解しておくべき基本的な概念です。
この後のセクションでは、これらの概念についてそれぞれ深く理解し、それらを一緒に使用する方法について説明します。
