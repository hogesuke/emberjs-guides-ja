<!--
Up to this point, we've generated four top level routes.
-->

これまでに、4つのトップレベルルートを作成しました。

<!--
* An `about` route, that gives information on our application.
* A `contact` route, with information on how to contact the company.
* A `rentals` route, where we will allow users to browse rental properties.
* The `index` route, which we've set up to redirect to the `rentals` route.
-->

* `about`ルート: アプリケーションについての情報
* `contact`ルート: お問い合わせ先に関する情報
* `rentals`ルート: 賃貸物件を閲覧するページ
* `index`ルート: `rentals`ルートにリダイレクト

<!--
Our `rentals` route is going to serve multiple functions.
From our [acceptance tests](../acceptance-test), we've shown that we want our users to be able to browse and search rentals, as well as see detailed information for individual rentals.
To satisfy that requirement, we are going to make use of Ember's [nested route capability](../../routing/defining-your-routes/#toc_nested-routes).
-->

`rentals`ルートにさらに機能を追加します。
[受け入れテスト](../acceptance-test)から、ユーザーが物件を閲覧したり、検索したり、個々の物件の詳細情報を参照できることを定義しました。
この要件を満たすために、Emberの[ルートのネスト(入れ子)機能](../../routing/defining-your-routes/#toc_nested-routes)を利用します。

<!--
By the end of this section we want to have created the following new routes:
-->

このセクションの最後には、以下の新しいルートの作成が完了しているでしょう。

<!--
* A `rentals/index` route that displays the rental page's general information, and also lists available rentals.
The index nested route is shown by default when the user visits the `rentals` URL.
* A `rentals/show` route that still displays the rental page's general information, while also showing detailed information about a selected rental.
The `show` route will get substituted with the id of the rental being shown (for example `rentals/grand-old-mansion`).
-->

* `rentals/index`ルート: 物件ページの一般情報を表示し、利用可能な物件を一覧表示する。
  ユーザーが `rentals` URLにアクセスすると、ネストしたインデックスルートがデフォルトで表示されます。
* `rentals/show`ルート: 物件ページの一般情報も表示しつつ、選択した物件に関する詳細情報を表示する。
  `show`ルートは、表示されている物件ID（例えば、`rentals/grand-old-mansion`(壮大な邸宅))に置き換えられます。


<!--
## The Parent Route
-->

## 親ルート

<!--
Previously, in the [Routes and Templates tutorial](../routes-and-templates), we set up a `rentals` route.
-->

以前に、[ルートとテンプレート](../routes-and-templates)チュートリアルで`rentals`ルートを設定していました。

<!--
Opening the template for this route reveals an outlet underneath the route's general page information.
At the bottom of the template, you'll notice an `{{outlet}}` helper.
This is where the active nested route will be rendered.
-->

このルートのテンプレートを開くと、一番下に`{{outlet}}`ヘルパーがあります。
outletはネストしたアクティブなルートが描画される場所です。

```app/templates/rentals.hbs
<div class="jumbo">
  <div class="right tomster"></div>
  <h2>Welcome!</h2>
  <p>We hope you find exactly what you're looking for in a place to stay.</p>
  {{#link-to 'about' class="button"}}
    About Us
  {{/link-to}}
</div>
{{#list-filter
   filter=(action 'filterByCity')
   as |filteredResults|}}
  <ul class="results">
    {{#each filteredResults as |rentalUnit|}}
      <li>{{rental-listing rental=rentalUnit}}</li>
    {{/each}}
  </ul>
{{/list-filter}}
{{outlet}}
```

<!--
Having a parent route means that any content on our parent route template will be present as we browse down through our child routes, allowing us to add things like common instructions, navigation, footers or sidebars.
-->

親ルートを持っているということは、親ルートのテンプレートのコンテンツは、子ルートを閲覧する際に存在することを意味し、ナビゲーション、フッターまたはサイドバーの共通のものを追加することができます。

<!--
## Generating a Nested Index Route
-->

## ネストしたインデックスルートの作成

<!--
The first nested route to generate will be the index route.
An index nested route works similarly to the base index route.
It is the default route that renders when no route is provided.
Therefore in our case, when we navigate to `/rentals`, Ember will attempt to load the rentals index route as a nested route.
-->

最初に作成するネストしたルートはインデックスルートです。
ネストしたインデックスルートは、ベースインデックスルートと同様に機能します。
これは、ルートが提供されていないときに描画されるデフォルトのルートです。
したがって、`/rentals`にナビゲートすると、Emberは`rentals/index`ルートをネストしたルートとしてロードしようとします。

<!--
To create an index nested route, run the following command:
-->

ネストしたインデックスルートを作成するには、次のコマンドを実行します。

```shell
ember g route rentals/index
```

<!--
If you open up your Router (`app/router.js`) you may notice that the rentals line has changed.
This extra `function() {}` is required because it needs a child route, the `this.route('index', { path: '/'});` is implied.
-->

ルーター(`app/router.js`)を開くと、rentalsの行が変更されていることが気がつくでしょう。この特別な`function() {}` は、子ルートが必要なので必須です。これは`this.route('index', { path: '/'});` 暗黙的に表しています。

```app/router.js
Router.map(function() {
  this.route('about');
  this.route('contact');
  this.route('rentals', function() {});
});
```

<!--
Much like how our application's `index` route doesn't appear in our Router, `index` routes on sub-routes won't explicitly appear in the Router either.
Ember knows that the default action is to take the user to the `index` route.
However, you can add the `index` route if you want to customize it.
For example, you can modify the `index` route's path by specifying `this.route('index', { path: '/custom-path'})`.
-->

アプリケーションの`index`ルートがルーターに書かれていないのと同じで、子ルートの`index`ルートもルーターに明示的に書くことはしません。
Emberはデフォルトのアクションがユーザーを`index`ルートに連れて行くことを知っています。
ただし、カスタマイズする場合は、`index`ルートを書きます。
たとえば、`this.route('index', { path: '/custom-path'})`と指定することで、インデックスルートのパスを変更できます。

<!--
In the section on [using Ember Data](../ember-data#toc_updating-the-model-hook), we added a call to fetch all rentals.
Let's implement our newly generated `rentals/index` route by moving this `findAll` call from the parent `rentals` route to our new sub-route.
-->

`rentals/index`ルートを実装していきましょう。
[Ember Dataを使う](../ember-data#toc_モデルフックの変更)のセクションでは、すべての物件を取得するよう実装しました。
その`findAll`を呼び出している部分を親ルート(rentals)から子ルート(`rentals/index`)に移します。

```app/routes/rentals.js{-4,-5,-6}
import Route from '@ember/routing/route';

export default Route.extend({
  model() {
    return this.get('store').findAll('rental');
  }
});
```

```app/routes/rentals/index.js{+4,+5,+6}
import Route from '@ember/routing/route';

export default Route.extend({
  model() {
    return this.get('store').findAll('rental');
  }
});
```

<!--
Now that we are returning all of our rentals to the nested route's model, we will also move the rental list markup from our main route template to our nested route index template.
-->

すべての物件をネストしたルートのモデルに戻したので、物件一覧のマークアップもメインルートのテンプレートからネストしたインデックスルートのテンプレートに移します。

```app/templates/rentals.hbs{-9,-10,-11,-12,-13,-14,-15,-16,-17}
<div class="jumbo">
  <div class="right tomster"></div>
  <h2>Welcome!</h2>
  <p>We hope you find exactly what you're looking for in a place to stay.</p>
  {{#link-to 'about' class="button"}}
    About Us
  {{/link-to}}
</div>
{{#list-filter
   filter=(action 'filterByCity')
   as |rentals|}}
  <ul class="results">
    {{#each rentals as |rentalUnit|}}
      <li>{{rental-listing rental=rentalUnit}}</li>
    {{/each}}
  </ul>
{{/list-filter}}
{{outlet}}
```

```app/templates/rentals/index.hbs{+1,+2,+3,+4,+5,+6,+7,+8,+9}
{{#list-filter
   filter=(action 'filterByCity')
   as |filteredResults|}}
  <ul class="results">
    {{#each filteredResults as |rentalUnit|}}
      <li>{{rental-listing rental=rentalUnit}}</li>
    {{/each}}
  </ul>
{{/list-filter}}
{{outlet}}
```


<!--
Finally, we need to make our controller that has our filter action available to the new nested index route.
-->

最後に、ネストしたインデックスルートで検索機能を使えるよう、コントローラを作成する必要があります。

<!--
Start by running the following command to create an index controller for our nested route: 
-->

次のコマンドを実行して、ネストしたインデックスルートのコントローラを作成します。

```shell
ember g controller rentals/index
```

<!--
Instead of copying the whole controller file over to `app/controllers/rentals/index.js` from `app/controllers/rentals.js`, we'll just take advantage of JavaScript's import/export feature to re-export the rentals controller as the rentals/index controller:
-->

単に`app/controllers/rentals.js`から`app/controllers/rentals/index.js`にファイル全体をコピーするのではなく、JavaScriptのインポート/エクスポート機能を利用して、rentalsコントローラを再エクスポートし、rentals/indexコントローラで使うやり方でやってみましょう:

```app/controllers/rentals/index.js{-1,+2,-4,-5,+6}
import Controller from '@ember/controller';
import RentalsController from '../rentals';

export default Controller.extend({
});
export default RentalsController;
```

<!--
## Setting up Data for the Nested Detail Route
-->

## ネストしたshowルートの設定

<!--
Next, we will want to create a sub-route that will list information for a specific rental.
To do this, we will need to update a couple of files.
To find a specific rental, we will want to use Ember Data's `findRecord` function [(see "Finding Records" for more details)](../../models/finding-records/).
The `findRecord` function requires that we search by a unique key. -->

次に、特定の物件の詳細情報を表示する子ルートを作成します。
そのためには、いくつかのファイルの変更が必要です。
特定の物件の情報を取得するには、Ember Dataの`findRecord`関数を使用します。[(詳しくは、「レコードの検索」を参照)](../../models/finding-records/)
`findRecord`関数は、ユニークなキーで検索する必要があります。

<!--
While on the `show` route, we will also want to show additional information about our specific rental.
-->

`show`ルートでは、物件の追加情報を表示するようにします。

<!--
In order to do this, we will need to modify the Mirage `config.js` file that we added
back in the [Installing Addons section](../installing-addons). We will add a new route
handler to return a specific rental:
-->

これを行うには、[アドオンのインストール](../installing-addons)のセクションで追加したMirageの`config.js`ファイルを変更する必要があります。
特定の物件を返すための新しいルートハンドラを追加します:

```mirage/config.js{+57,+58,+59,+60}
export default function() {
  this.namespace = '/api';

  let rentals = [
    {
      type: 'rentals',
      id: 'grand-old-mansion',
      attributes: {
        title: "Grand Old Mansion",
        owner: "Veruca Salt",
        city: "San Francisco",
        category: "Estate",
        bedrooms: 15,
        image: "https://upload.wikimedia.org/wikipedia/commons/c/cb/Crane_estate_(5).jpg",
        description: "This grand old mansion sits on over 100 acres of rolling hills and dense redwood forests."
      }
    },
    {
      type: 'rentals',
      id: 'urban-living',
      attributes: {
        title: "Urban Living",
        owner: "Mike Teavee",
        city: "Seattle",
        category: "Condo",
        bedrooms: 1,
        image: "https://upload.wikimedia.org/wikipedia/commons/0/0e/Alfonso_13_Highrise_Tegucigalpa.jpg",
        description: "A commuters dream. This rental is within walking distance of 2 bus stops and the Metro."
      }
    },
    {
      type: 'rentals',
      id: 'downtown-charm',
      attributes: {
        title: "Downtown Charm",
        owner: "Violet Beauregarde",
        city: "Portland",
        category: "Apartment",
        bedrooms: 3,
        image: "https://upload.wikimedia.org/wikipedia/commons/f/f7/Wheeldon_Apartment_Building_-_Portland_Oregon.jpg",
        description: "Convenience is at your doorstep with this charming downtown rental. Great restaurants and active night life are within a few feet."
      }
    }
  ];

  this.get('/rentals', function(db, request) {
    if (request.queryParams.city !== undefined) {
      let filteredRentals = rentals.filter(function (i) {
        return i.attributes.city.toLowerCase().indexOf(request.queryParams.city.toLowerCase()) !== -1;
      });
      return { data: filteredRentals };
    } else {
      return { data: rentals };
    }
  });

  // Find and return the provided rental from our rental list above
  this.get('/rentals/:id', function (db, request) {
    return { data: rentals.find((rental) => request.params.id === rental.id) };
  });

}
```

<!--
## Generating a Nested Detail Route
-->

## ネストしたshowルートの作成

<!--
Now that our API is ready to return individual rentals, we can generate our `show` sub-route.
Much like generating our `rentals` route, we will use `ember g` to create a nested route.
-->

APIが個々の物件を返す準備ができたので、子ルートの`show`ルートを作成しましょう。
`rentals`ルートの時と同じように、`ember g`を使ってネストしたルートを作成します。

```shell
ember g route rentals/show
```

<!--
You will see output like this:
-->

次のような出力が表示されます。

```shell
installing route
  create app/routes/rentals/show.js
  create app/templates/rentals/show.hbs
updating router
  add route rentals/show
installing route-test
  create tests/unit/routes/rentals/show-test.js
```

<!--
Let's start by looking at the changes to our Router (`app/router.js`).
-->

まず、ルータ(`app/router.js`)への変更を見てみましょう。


```app/router.js{+5}
Router.map(function() {
  this.route('about');
  this.route('contact');
  this.route('rentals', function() {
    this.route('show');
  });
});
```

<!--
Our new route is nested within our `rentals` route.
This tells Ember that it is a sub-route and will be accessed through `localhost:4200/rentals/show`.
-->

作成したルートは`rentals`ルート内に入れ子になっています。
これはEmberに、それが子ルートであり、`localhost:4200/rentals/show`を通してアクセスされることを伝えています。

<!--
To tell the application which rental we want to access, we need to replace the `show` route path with the ID of the rental listing.
We also want to simplify the URL so that it looks more like this: `localhost:4200/rentals/id-for-rental`.
-->

アプリケーションにアクセスしたい物件を伝えるには、物件のIDで`show`ルートのパスを置き換える必要があります。 
それと、URLは`localhost:4200/rentals/物件ID`のように簡略化させます。

<!--
To do that, we modify our route as follows:
-->

そのために、次のようにルートを修正します:

```app/router.js{-5,+6}
Router.map(function() {
  this.route('about');
  this.route('contact');
  this.route('rentals', function() {
    this.route('show');
    this.route('show', { path: '/:rental_id' });
  });
});
```

<!--
The `rental_id` will now be passed to the route.
-->

これにより、`rental_id`がルートに渡されます。


<!--
## Finding By ID
-->

## IDで検索

<!--
Next, we want to edit our `show` route to retrieve the requested rental:
-->

次に、 `show`ルートを編集して、リクエストされた物件を取得します。

```app/routes/rentals/show.js{+4,+5,+6}
import Route from '@ember/routing/route';

export default Route.extend({
  model(params) {
    return this.get('store').findRecord('rental', params.rental_id);
  }
});
```

<!--
Since we added `:rental_id` to the `show` path in our router, `rental_id` is now available in our `model` hook.
When we call `this.get('store').findRecord('rental', params.rental_id)`, Ember Data queries `/rentals/our-id` using a HTTP GET request ([learn more about that here](../../models/)).
-->

ルータで、`show`のパスに`:rental_id`を追加したので、`model`フックで`rental_id`を利用できるようになりました。
`this.get('store').findRecord('rental', params.rental_id)`が呼ばれると、Ember DataはHTTP GETリクエスト`/rentals/物件ID`を発行します。([詳細はこちら](../../models/))

<!--
## Adding the Rental To Our Template
-->

## テンプレートに物件を追加する

<!--
Next, we can update the template for our show route (`app/templates/rentals/show.hbs`) and list the information for our rental.
-->

次に、`show`ルートのテンプレート(`app/templates/rentals/show.hbs`)を変更し、物件の詳細情報を表示します。

```app/templates/rentals/show.hbs{+1,+2,+3,+4,+5,+6,+7,+8,+9,+10,+11,+12,+13,+14,+15,+16,+17,+18,+19,-20}
<div class="jumbo show-listing">
  <h2 class="title">{{model.title}}</h2>
  <div class="right detail-section">
    <div class="detail owner">
      <strong>Owner:</strong> {{model.owner}}
    </div>
    <div class="detail">
      <strong>Type:</strong> {{rental-property-type model.category}} - {{model.category}}
    </div>
    <div class="detail">
      <strong>Location:</strong> {{model.city}}
    </div>
    <div class="detail">
      <strong>Number of bedrooms:</strong> {{model.bedrooms}}
    </div>
    <p class="description">{{model.description}}</p>
  </div>
  <img src="{{model.image}}" class="rental-pic">
</div>
{{outlet}}
```

<!--
Now browse to `localhost:4200/rentals/grand-old-mansion` and you should see the information listed for that specific rental.
-->

`localhost:4200/rentals/grand-old-mansion`を参照すると、1物件の情報が表示されます。

![Rental Page Nested Show Route](../../images/subroutes/subroutes-super-rentals-show.png)

<!--
## Linking to a Specific Rental
-->

## 物件詳細ページにリンクする

<!--
Now that we can load pages for individual rentals, we'll add a link (using the `link-to` helper) within our `rental-listing` component to navigate to individual pages.
Here, the `link-to` helper takes the route name and the rental model object as arguments.
When you pass an object as second argument into the `link-to` block helper, it will by default [serialize](https://www.emberjs.com/api/ember/2.16/classes/Route/methods/beforeModel?anchor=serialize) the object to the ID of the model into the URL.
Alternately, you may just pass `rental.id` for clarity.
-->

個々の物件のページを読み込むことができるようになりましたので、`rental-listing`コンポーネント内にリンク( `link-to`ヘルパーを使用)を追加して個々のページに遷移できるようにします。
`link-to`ヘルパーには、ルート名とRentalモデルのインスタンスを引数として渡します。
`link-to`ヘルパーの2番目の引数にオブジェクトを渡すると、デフォルトでモデルのIDがURLに[シアライズ](https://www.emberjs.com/api/ember/2.16/classes/Route/methods/beforeModel?anchor=serialize)されます。
わかりやすくするために`rent.id`を渡すこともできます。

<!--
Clicking on the title will load the detail page for that rental.
-->

タイトルをクリックすると、その物件の詳細ページが読み込まれるようになります。

```app/templates/components/rental-listing.hbs{-6,+7}
<article class="listing">
  <a {{action 'toggleImageSize'}} class="image {{if isWide "wide"}}">
    <img src="{{rental.image}}" alt="">
    <small>View Larger</small>
  </a>
  <h3>{{rental.title}}</h3>
  <h3>{{#link-to "rentals.show" rental}}{{rental.title}}{{/link-to}}</h3>
  <div class="detail owner">
    <span>Owner:</span> {{rental.owner}}
  </div>
  <div class="detail type">
    <span>Type:</span> {{rental-property-type rental.category}}
      - {{rental.category}}
  </div>
  <div class="detail location">
    <span>Location:</span> {{rental.city}}
  </div>
  <div class="detail bedrooms">
    <span>Number of bedrooms:</span> {{rental.bedrooms}}
  </div>
  {{location-map location=rental.city}}
</article>
```
![Rental Page Nested Index Route](../../images/subroutes/subroutes-super-rentals-index.png)

<!--
At this point you can do a [deployment](../deploying/) and share your Super Rentals application to the world
or you can use this as a base to explore other Ember features and addons.
Regardless, we hope this has helped you get started with creating your own ambitious applications with Ember!
-->

お疲れ様でした！
この時点であなたはEmberを使えるようになっているはずです。
Super Rentalsを[デプロイ](../deploying/)して、世界中に共有したり、これをベースとして他のEmberの機能やアドオンを探索したりできますね。
これからEmberを使って野心的なアプリケーションの開発をする際、このチュートリアルがあなたの手助けになっていれば幸いです。

<!--
### Acceptance Tests
-->

### 受け入れテスト

<!--
We want to verify that we can click on a specific rental and load a detailed view to the page.
We'll click on the title and validate that an expanded description of the rental is shown. 
-->

特定の物件をクリックしてページの詳細ページを読み込むことができることを確認したいと思います。
タイトルをクリックし、物件の詳細情報が表示されることを確認します。

```/tests/acceptance/list-rentals-test.js{+2,+3,+4,+5,+6,+7,+8}
test('should show details for a specific rental', function (assert) {
  visit('/rentals');
  click('a:contains("Grand Old Mansion")');
  andThen(function() {
    assert.equal(currentURL(), '/rentals/grand-old-mansion', 'should navigate to show route');
    assert.equal(find('.show-listing h2').text(), "Grand Old Mansion", 'should list rental title');
    assert.equal(find('.description').length, 1, 'should list a description of the property');
  });
});
```

<!--
At this point all our tests should pass, including the [list of acceptance tests](../acceptance-test) we created as our beginning requirements.
-->

この時点で、最初に要件定義した受け入れテストを含む、すべてのテストがパスしているはずです。

![Acceptance Tests Pass](../../images/subroutes/all-acceptance-pass.png)
