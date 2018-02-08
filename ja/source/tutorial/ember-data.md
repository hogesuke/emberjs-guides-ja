<!--
Currently, our app is using hard-coded data for our rental listings, defined in the `rentals` route handler.
As our application grows, we will want to persist our rental data on a server, and make it easier to do advanced operations on the data, such as querying.
-->

現在のところ、rentalsルートハンドラにハードコードされたデータを物件一覧に使用していますが、アプリケーション開発を続けていると、物件データをサーバー上に残し、検索などのデータの高度な操作を簡単にしたくなります。


<!--
Ember comes with a data management library called [Ember Data](https://github.com/emberjs/data) to help deal with persistent application data.
-->

Emberには、永続的なアプリケーションデータを扱うための[Ember Data](https://github.com/emberjs/data)というデータ管理ライブラリが付属しています。

<!--
Ember Data requires you to define the structure of the data you wish to provide to your application by extending [`DS.Model`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Model).
-->

Ember Dataを使うには、[`DS.Model`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Model)を拡張し、アプリケーションに提供したいデータの構造を定義する必要があります。

<!--
You can generate an Ember Data Model using Ember CLI.
We'll call our model `rental` and generate it as follows:
-->

Ember CLIを使用してEmberデータモデルを生成できます。 モデルのレンタルを呼び出して、次のように生成します。

```shell
ember g model rental
```

<!--
This results in the creation of a model file and a test file:
-->

この結果、モデルのファイルとテストファイルが作成されます。

```shell
installing model
  create app/models/rental.js
installing model-test
  create tests/unit/models/rental-test.js
```

<!--
When we open the model file, we can see a blank class extending [`DS.Model`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Model):
-->

モデルのファイルを開くと、DS.Modelを拡張した空のクラスが書かれています。

```app/models/rental.js
import DS from 'ember-data';

export default DS.Model.extend({

});
```

<!--
Let's define the structure of a rental object using the same attributes for our rental that we [previously used](../model-hook/) in our hard-coded array of JavaScript objects -
_title_, _owner_, _city_, _category_, _image_, _bedrooms_ and _description_.
Define attributes by giving them the result of the function [`DS.attr()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS/methods/attr?anchor=attr).
For more information on Ember Data Attributes, read the section called [Defining Attributes](../../models/defining-models/#toc_defining-attributes) in the guides.
-->

[以前](../model-hook/)ハードコードしたオブジェクトと同じ属性を持つモデルを定義しましょう。属性は_title_、_owner_、_city_、_category_、_image_、_bedrooms_、_description_です。 [`DS.attr()`](https://www.emberjs.com/api/ember-data/2.16/classes/DS/methods/attr?anchor=attr)関数にその関数が返す型を渡して属性を定義します。(省略もできるため、下記のコードでは省略されています。) Emberのデータ属性の詳細については、ガイドの[属性の定義](../../models/defining-models/#toc_defining-attributes)を参照してください。

```app/models/rental.js{+4,+5,+6,+7,+8,+9,+10}
import DS from 'ember-data';

export default DS.Model.extend({
  title: DS.attr(),
  owner: DS.attr(),
  city: DS.attr(),
  category: DS.attr(),
  image: DS.attr(),
  bedrooms: DS.attr(),
  description: DS.attr()
});
```

<!--
We now have a model object that we can use for our Ember Data implementation.
-->

これでEmber Dataで使えるモデルが用意できました。

<!--
### Updating the Model Hook
-->

### モデルフックの変更

<!--
To use our new Ember Data Model object, we need to update the `model` function we [previously defined](../model-hook/) in our route handler.
Delete the hard-coded JavaScript Array, and replace it with the following call to the [Ember Data Store service](../../models/#toc_the-store-and-a-single-source-of-truth).
The [store service](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Store) is injected into all routes and their corresponding controllers in Ember.
It is the main interface you use to interact with Ember Data.
In this case, call the [`findAll`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Store/methods/findAll?anchor=findAll) function on the store and provide it with the name of your newly created rental model class.
-->

作成したモデルを使用するには、[以前にルートハンドラで定義したmodel関数](../model-hook/)を変更する必要があります。 ハードコードされたJavaScript配列を削除し、[Ember Data Storeサービス](../../models/#toc_the-store-and-a-single-source-of-truth)を使うように以下を書き換えてください。 Storeサービスは、Emberのすべてのルートとルートのコントローラにインジェクト(注入)されています。 StoreはEmber Dataとやりとりするために使用するメインインターフェイスです。 今回は、Storeの[`findAll`](https://www.emberjs.com/api/ember-data/2.16/classes/DS.Store/methods/findAll?anchor=findAll)関数に作成したRentalモデルクラスの名前を指定して実行します。

```app/routes/rentals.js{+5,-6,-7,-8,-9,-10,-11,-12,-13,-14,-15,-16,-17,-18,-19,-20,-21,-22,-23,-24,-25,-26,-27,-28,-29,-30,-31,-32,-33}
import Route from '@ember/routing/route';

export default Route.extend({
  model() {
    return this.get('store').findAll('rental');
    return [{
      id: 'grand-old-mansion',
      title: 'Grand Old Mansion',
      owner: 'Veruca Salt',
      city: 'San Francisco',
      category: 'Estate',
      bedrooms: 15,
      image: 'https://upload.wikimedia.org/wikipedia/commons/c/cb/Crane_estate_(5).jpg',
      description: "This grand old mansion sits on over 100 acres of rolling hills and dense redwood forests."
    }, {
      id: 'urban-living',
      title: 'Urban Living',
      owner: 'Mike TV',
      city: 'Seattle',
      category: 'Condo',
      bedrooms: 1,
      image: 'https://upload.wikimedia.org/wikipedia/commons/0/0e/Alfonso_13_Highrise_Tegucigalpa.jpg',
      description: "A commuters dream. This rental is within walking distance of 2 bus stops and the Metro."
    }, {
      id: 'downtown-charm',
      title: 'Downtown Charm',
      owner: 'Violet Beauregarde',
      city: 'Portland',
      category: 'Apartment',
      bedrooms: 3,
      image: 'https://upload.wikimedia.org/wikipedia/commons/f/f7/Wheeldon_Apartment_Building_-_Portland_Oregon.jpg',
      description: "Convenience is at your doorstep with this charming downtown rental. Great restaurants and active night life are within a few feet."
    }];
  }
});
```

<!--
When we call `findAll`, Ember Data will attempt to fetch rentals from `/api/rentals`.
If you recall, in the section titled [Installing Addons](../installing-addons/) we set up an adapter to route data requests through `/api`.
-->

findAllを実行すると、Ember Dataは`/api/rentals`から物件を取得しようとします。 `/api`が付くのは、[アドオンのインストール](../installing-addons/) セクションで、リクエストが`/api`を介すようアダプタを設定したためです。

<!--
You can read more about Ember Data in the [Models section](../../models/).
-->

Ember Dataの詳細については、[モデル](../../models/)セクションを参照してください。

<!--
Since we have already set up Ember Mirage in our development environment, Mirage will return the data we requested without actually making a network request.
-->

すでにEmber Mirageを開発環境にセットアップしたため、Mirageが要求されたデータを返します。ネットワークリクエストは実際には行われません。

<!--
When we deploy our app to a production server,
we will likely want to replace Mirage with a remote server for Ember Data to communicate with for storing and retrieving persisted data.
A remote server will allow for data to be shared and updated across users.
-->

アプリをプロダクションサーバーにデプロイする時など、
Ember Dataが永続化されたデータの保存や検索の通信をするように、Mirageからリモートサーバに置き換えることもできます。
リモートサーバーを使えば、ユーザー間でデータの共有や更新ができるようになります。
