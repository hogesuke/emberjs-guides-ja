<!--
Ember has a rich ecosystem of addons that can be easily added to projects.
Addons provide a wide range of functionality to projects, often saving time and letting you focus on your project.
-->

Emberには、プロジェクトに簡単に追加できる豊富なエコシステムがあります。
アドオンは、プロジェクトに幅広い機能を提供し、時間を節約し、プロジェクトに集中することができます。

<!--
To browse addons, visit the [Ember Observer](https://emberobserver.com/) website.  It catalogs and categorizes Ember addons that have been published to NPM and assigns them a score based on a variety of criteria.
-->

アドオンを参照するには、[Ember Observer](https://emberobserver.com/)を参照してください。 NPMに公開されているEmberアドオンをカタログ化しカテゴリー毎に分け、さまざまな基準に基づいてスコアを割り当てます。

<!--
For Super Rentals, we'll take advantage of two addons: [ember-cli-tutorial-style](https://github.com/toddjordan/ember-cli-tutorial-style) and [ember-cli-mirage](http://www.ember-cli-mirage.com/).
-->

Super Rentalsでは、[ember-cli-tutorial-style](https://github.com/toddjordan/ember-cli-tutorial-style)と[ember-cli-mirage](http://www.ember-cli-mirage.com/)の2つのアドオンを使います。

<!--
### ember-cli-tutorial-style
-->

### ember-cli-tutorial-styleアドオン

<!--
Instead of having you copy/paste in CSS to style Super Rentals, we've created an addon called [ember-cli-tutorial-style](https://github.com/ember-learn/ember-cli-tutorial-style) that instantly adds CSS to the tutorial.
The addon works by generating a file called `ember-tutorial.css` and putting that file in the super-rentals `vendor` directory.
-->

チュートリアルの読者のために、Super RentalsのスタイルにCSSをコピー/ペーストさせるのではなく、即座にCSSをチュートリアルに追加する[ember-cli-tutorial-style]というアドオンを用意しています。
このアドオンは、`ember-tutorial.css`というファイルを生成し、そのファイルをプロジェクトのvendorディレクトリに入れて動作します。

<!--
The [`vendor` directory](../../addons-and-dependencies/managing-dependencies/#toc_other-assets) in Ember is a special directory where you can include content that gets compiled into your application.
-->

Emberの[`vendor`ディレクトリ](../../addons-and-dependencies/managing-dependencies/#toc_other-assets)は、アプリケーションにコンパイルされるコンテンツを含めることができる特別なディレクトリです。

<!--
As Ember CLI runs, it takes the `ember-tutorial` CSS file and puts it in a file called `vendor.css`.
The `vendor.css` file is referenced in `app/index.html`, making the styles available at runtime.
-->

Ember CLIが実行されると、`ember-tutorial`CSSファイルが`vendor.css`というファイルに置かれます。
`vendor.css`ファイルは` app/index.html`で参照され、実行時にスタイルを利用可能にします。

<!--
We can make additional style tweaks to `vendor/ember-tutorial.css`, and the changes will take effect whenever we restart the app.
-->

`vendor/ember-tutorial.css`は手を加えることができます。変更を反映するにはアプリケーションの再起動が必要です。

<!--
Run the following command to install the addon:
-->

次のコマンドを実行してアドオンをインストールします:


```shell
ember install ember-cli-tutorial-style
```

<!--
Here is the output:
-->

出力は以下のようになります。

```shell
NPM: Installed ember-cli-tutorial-style
installing ember-cli-tutorial-style
  create public/assets/images/teaching.png
  create vendor/ember-tutorial.css
Installed addon package.
```

<!--
Since Ember addons are npm packages, `ember install` installs them in the `node_modules` directory, and makes an entry
in `package.json`. Be sure to restart your server after the addon has installed successfully. Restarting the server will
incorporate the new CSS and refreshing the browser window will give you this:
-->

Emberアドオンはnpmパッケージなので、`ember install`はアドオンを`node_modules`ディレクトリにインストールし、`package.json`にエントリを作成します。 アドオンが正常にインストールされたら、必ずサーバーを再起動してください。 サーバーを再起動すると新しいCSSが組み込まれ、ブラウザウィンドウを更新すると次のようになります。

![super rentals styled homepage](../../images/installing-addons/styled-super-rentals-basic.png)

<!--
### ember-cli-mirage
-->

### ember-cli-mirageアドオン

<!--
[Mirage](http://www.ember-cli-mirage.com/) is a client HTTP stubbing library often used for Ember application testing.
For the case of this tutorial, we'll use mirage as our source of data rather than a traditional backend server.
Mirage will allow us to create fake data to work with while developing our app and mimic an API.
The data and endpoints we setup here will come into play later in the tutorial, when we use Ember Data to make server requests.
-->

[Mirage](http://www.ember-cli-mirage.com/)(ミラージュ)は、Emberのアプリケーションテストによく使用されるクライアントのHTTPスタブライブラリです。
このチュートリアルでは、伝統的なバックエンドサーバーではなく、データソースとしてMirageを使用します。
Mirageで、開発用に偽のデータとAPIを作成できます。
ここで設定するデータとエンドポイントは、このチュートリアルの後半の「Ember Dataを使用してサーバーリクエストを行う」で説明されています。

<!--
Install the Mirage addon as follows:
-->

以下のコマンドを実行してMirageをインストールします。

```shell
ember install ember-cli-mirage
```

<!--
Our primary focus with mirage will be in the `config.js` file, which is where we can define our API endpoints and our data.
We will be following the [JSON-API specification](http://jsonapi.org/) which requires our data to be formatted a certain way.
Let's configure Mirage to send back our rentals that we had defined above by updating `mirage/config.js`:
-->

Mirageでまず着目すべき点は、APIエンドポイントとデータを定義できる`config.js`ファイルです。
JSONデータの形式は[JSON-API](http://jsonapi.org/)の仕様に合わせています。
`mirage/config.js`を更新して、上記で定義したレンタルを返送するようにMirageを設定しましょう：

```mirage/config.js{+1,+2,+3,+4,+5,+6,+7,+8,+9,+10,+11,+12,+13,+14,+15,+16,+17,+18,+19,+20,+21,+22,+23,+24,+25,+26,+27,+28,+29,+30,+31,+32,+33,+34,+35,+36,+37,+38,+39,+40,+41,+42,-43,-44,-45,-46,-47,-48,-49,-50,-51,-52,-53,-54,-55,-56,-57,-58,-59,-60,-61,-62,-63,-64,-65,-66,-67}
export default function() {
  this.namespace = '/api';

  this.get('/rentals', function() {
    return {
      data: [{
        type: 'rentals',
        id: 'grand-old-mansion',
        attributes: {
          title: 'Grand Old Mansion',
          owner: 'Veruca Salt',
          city: 'San Francisco',
          category: 'Estate',
          bedrooms: 15,
          image: 'https://upload.wikimedia.org/wikipedia/commons/c/cb/Crane_estate_(5).jpg'
        }
      }, {
        type: 'rentals',
        id: 'urban-living',
        attributes: {
          title: 'Urban Living',
          owner: 'Mike Teavee',
          city: 'Seattle',
          category: 'Condo',
          bedrooms: 1,
          image: 'https://upload.wikimedia.org/wikipedia/commons/0/0e/Alfonso_13_Highrise_Tegucigalpa.jpg'
        }
      }, {
        type: 'rentals',
        id: 'downtown-charm',
        attributes: {
          title: 'Downtown Charm',
          owner: 'Violet Beauregarde',
          city: 'Portland',
          category: 'Apartment',
          bedrooms: 3,
          image: 'https://upload.wikimedia.org/wikipedia/commons/f/f7/Wheeldon_Apartment_Building_-_Portland_Oregon.jpg'
        }
      }]
    };
  });
}
export default function() {
 // These comments are here to help you get started. Feel free to delete them.

  /*
    Config (with defaults).

    Note: these only affect routes defined *after* them!
  */

  // this.urlPrefix = '';    // make this `http://localhost:8080`, for example, if your API is on a different server
  // this.namespace = '';    // make this `/api`, for example, if your API is namespaced
  // this.timing = 400;      // delay for each request, automatically set to 0 during testing

  /*
    Shorthand cheatsheet:

    this.get('/posts');
    this.post('/posts');
    this.get('/posts/:id');
    this.put('/posts/:id'); // or this.patch
    this.del('/posts/:id');

    http://www.ember-cli-mirage.com/docs/v0.3.x/shorthands/
  */
}
```

<!--
Mirage works by overriding the JavaScript code that makes network requests and instead returns the JSON you specify.
We should note that this means you will not see any network requests in your development tools but will instead see the JSON logged in your console.
Our update to `mirage/config.js` configures Mirage so that whenever Ember Data makes a GET request to `/api/rentals`, Mirage will return this JavaScript object as JSON and no network request is actually made.
We also specified a `namespace` of `/api` in our mirage configuration.
Without this change, navigation to `/rentals` in our application would conflict with Mirage.
-->

Mirageは、ネットワークリクエストを作成するJavaScriptコードをオーバーライドし、代わりに指定したJSONを返します。
これは、開発ツールでネットワークリクエストが表示されず、代わりにコンソールにJSONがログ表示されることに注意してください。
上記の`mirage/config.js`の変更で、Ember Dataが`api/rentals`にGETリクエストを行うたびに、ハードコードしたJavaScriptオブジェクトをJSONとして返し、実際にネットワークリクエストは行われないようにMirageを設定しています。
Mirageの設定で`/api`の`namespace`を指定しています。
この変更がなければ、アプリケーションの`/rentals`へのナビゲーションはMirageと競合します。

<!--
In order for this to work, we need our application to default to making requests to the namespace of `/api`.
To do this, we want to generate an application adapter.
An [Adapter](../../models/customizing-adapters) is an object that [Ember Data](../../models) uses to determine how we communicate with our backend.
We will cover Ember Data in more detail later in this tutorial.
For now, let's generate an adapter for our application:
-->

上記の変更を動作させるためには、デフォルトで`/api`という名前空間へのリクエストを行うよう、アプリケーションの設定が必要です。
これを行うには、アプリケーションアダプターを生成する必要があります。
[アダプター]（../../models/customizing-adaptapters）は、[Ember Data]（../../models）がバックエンドと通信する方法を決定するために使用します。
Ember Dataについては、このチュートリアルの後半で詳しく説明します。
今はアプリケーション用のアダプターを生成しましょう。

```shell
ember generate adapter application
```

<!--
This adapter will extend the [`JSONAPIAdapter`](https://www.emberjs.com/api/ember-data/release/classes/DS.JSONAPIAdapter) base class from Ember Data:
-->

アプリケーションアダプターはEmber Dataから[`JSONAPIAdapter`](https://www.emberjs.com/api/ember-data/release/classes/DS.JSONAPIAdapter)基底クラスを拡張しています。

```app/adapters/application.js{+4}
import DS from 'ember-data';

export default DS.JSONAPIAdapter.extend({
  namespace: 'api'
});
```

<!--
If you were running `ember serve` or `ember test --serve` in another shell, restart each of those servers to include Mirage in your build.
-->

別のシェルで`ember serve`または`ember test --serve`を実行している場合は、Mirageをビルドに含めるよう、サーバを再起動してください。

<!--
Note that at this point of the tutorial, the data is still provided by the `app/routes/rentals.js` file. We will make use of the mirage data we set up here in the upcoming section called [Using Ember Data](../ember-data/).
-->

チュートリアルのこの時点では、データは`app/routes/rentals.js`ファイルによって提供されていることに注意してください。この先の[Ember Dataを使う](../ember-data/)セクションで設定したMirageのデータを使用します。
