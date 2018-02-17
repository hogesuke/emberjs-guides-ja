<!--
For Super Rentals, we want to arrive at a home page which displays a list of rentals.
From there, we should be able to visit an about page and our contact page.
-->

Super Rentalsのトップページに物件一覧を表示します。トップページからはAboutページとお問い合わせページに行けるようにしましょう。

<!--
## An About Route
-->

## Aboutルート

<!--
Let's start by building our "about" page.
-->

まず、「about」ページを構築しましょう。

<!--
In Ember, when we want to make a new page that can be visited using a URL,
we need to generate a "route" using Ember CLI. For a quick overview of how
Ember structures things, see [our diagram on the Core Concepts page](../../getting-started/core-concepts/).
-->

Emberでは、URLを使用して訪問できる新しいページを作成する場合、Ember CLIを使用して「ルート」を作成する必要があります。 Emberアプリの構造の概要は、[コアコンセプト](../../getting-started/core-concepts/)ページの図を参照してください。

<!--
Let's use Ember's route generator to start our `about` route.
-->

Emberのルートジェネレータを使用してaboutルートを作りましょう。

```shell
ember generate route about
```

<!--
or for short,
-->

または短く、

```shell
ember g route about
```

<!--
_Note: Running `ember help generate` will list a number of other Ember resources you can create as well ..._
-->

_注: `ember help generate`を実行すると、作成できる他のEmberリソースも一覧表示されます。_

<!--
And here's what our generator prints out:
-->

ジェネレータが以下のように出力します。

```shell
installing route
  create app/routes/about.js
  create app/templates/about.hbs
updating router
  add route about
installing route-test
  create tests/unit/routes/about-test.js
```

<!--
An Ember route is built with three parts:
-->

Emberルートは3つの部分で構築されています。

<!--
1. An entry in the Ember router (`/app/router.js`), which maps between our route name and a specific URI
2. A route handler file, which sets up what should happen when that route is loaded _`(app/routes/about.js)`_
3. A route template, which is where we display the actual content for the page _`(app/templates/about.hbs)`_
-->

1. Emberルータ(`/app/router.js`)内のエントリ(ルート名と特定のURIとの間をマッピングします)
2. そのルートがロードされたときに何が起こるべきかを設定するルートハンドラファイル_`(app/routes/about.js)`_
3. ルートテンプレート。ページに表示する実際のコンテンツ_`(app/templates/about.hbs)`_

<!--
If we open `/app/router.js`, we'll see a new line of code for the **about** route, calling
`this.route('about')` in the `Router.map` function. That new line of code tells the Ember router
to run our `/app/routes/about.js` file when a visitor navigates to `/about`.
-->

`/app/router.js`を開くと、**about**ルートの新しいコード行が表示され、`Router.map`関数内に`this.route('about')`が呼ばれていることが確認できます。
その行は、ユーザが `/about`に移動したときにEmberルータに`/app/routes/about.js`ファイルを実行するよう指示します。

```app/router.js{+10}
import EmberRouter from '@ember/routing/router';
import config from './config/environment';

const Router = EmberRouter.extend({
  location: config.locationType,
  rootURL: config.rootURL
});

Router.map(function() {
  this.route('about');
});

export default Router;
```

<!--
Because we only plan to display static content on our about page, we won't adjust the `/app/routes/about.js`
route handler file right now. Instead, let's open our `/app/templates/about.hbs` template file and add some info about
Super Rentals:
-->

aboutページには、静的なコンテンツだけの表示に留めるため、`/app/routes/about.js`ルートハンドラファイルに手を加える必要はありません。代わりに、テンプレートファイル(`/app/templates/about.hbs`)を開き、Super Rentalsに関する情報を追加します。

```app/templates/about.hbs
<div class="jumbo">
  <div class="right tomster"></div>
  <h2>About Super Rentals</h2>
  <p>
    The Super Rentals website is a delightful project created to explore Ember.
    By building a property rental site, we can simultaneously imagine traveling
    AND building Ember applications.
  </p>
</div>
```

<!--
Now run `ember serve` (or `ember server`, or even `ember s` for short) on your command line to start
the Ember development server and then go to [`http://localhost:4200/about`](http://localhost:4200/about) to
see our new page in action!
-->

`ember serve`(または`ember server`、あるいは短く`ember s`)でEmber開発サーバを起動し、 [`http://localhost:4200/about`](http://localhost:4200/about)に行って実際のページを確認してください。

<!--
## A Contact Route
-->

## Contactルート(お問い合わせ)

<!--
Now let's create another route with contact details for the company.
Once again, we'll start by generating a route:
-->

次に、問い合わせ先の詳細を含む別のルートを作成しましょう。
もう一度、ルートを生成するところから始めます。

```shell
ember g route contact
```

<!--
Here again, we add a new `contact` route in `app/router.js` and generate a route handler in `app/routes/contact.js`.
-->

ここでもまた、`app/router.js`に新しい`contact`ルート(エントリ)を追加し、`app/routes/contact.js`にルートハンドラを生成します。

<!--
In the route template `/app/templates/contact.hbs`, let's add our contact details:
-->

ルートテンプレート `/app/templates/contact.hbs`に、問い合わせ先の詳細を追加しましょう。


```app/templates/contact.hbs
<div class="jumbo">
  <div class="right tomster"></div>
  <h2>Contact Us</h2>
  <p>Super Rentals Representatives would love to help you<br>choose a destination or answer
    any questions you may have.</p>
  <p>
    Super Rentals HQ
    <address>
      1212 Test Address Avenue<br>
      Testington, OR 97233
    </address>
    <a href="tel:503.555.1212">+1 (503) 555-1212</a><br>
    <a href="mailto:superrentalsrep@emberjs.com">superrentalsrep@emberjs.com</a>
  </p>
</div>
```

<!--
Now when we go to [`http://localhost:4200/contact`](http://localhost:4200/contact), we'll see our contact page.
-->

[http://localhost:4200/contact](http://localhost:4200/contact)に行くと、お問い合わせページが表示されます。

<!--
## Navigating with Links and the {{link-to}} Helper
-->

## リンクと{{link-to}}ヘルパーのナビゲーション

<!--
Moving around our site is a bit of a pain right now, so let's make that easier.
We'll put a link to the contact page on the about page, and a corresponding link to the about
page on the contact page.
-->

サイト内を移動するのが今のところ少し面倒なので、簡単にしましょう。
aboutページにお問い合わせページへのリンクと、お問い合わせページにaboutページへの対応するリンクを貼り付けます。

<!--
To do that, we'll use a [`{{link-to}}`](../../templates/links/) helper that Ember provides
that makes it easy to link between our routes.  Let's adjust our `about.hbs` file:
-->

Emberが提供する [`{{link-to}}`](../../templates/links/)ヘルパーを使用すれば、ルート間のリンクは簡単にできます。
`about.hbs`ファイルを以下のように変更してください。

```app/templates/about.hbs{+9,+10,+11}
<div class="jumbo">
  <div class="right tomster"></div>
  <h2>About Super Rentals</h2>
  <p>
    The Super Rentals website is a delightful project created to explore Ember.
    By building a property rental site, we can simultaneously imagine traveling
    AND building Ember applications.
  </p>
  {{#link-to "contact" class="button"}}
    Contact Us
  {{/link-to}}
</div>
```

<!--
In this case, we're telling the `{{link-to}}` helper the name of the route we want to link to: `contact`.
When we look at our about page at [`http://localhost:4200/about`](http://localhost:4200/about), we now have
a working link to our contact page:
-->

`{{link-to}}`ヘルパーに、リンクしたいルートの名前`contact`を渡しています。
[`http://localhost:4200/about`](http://localhost:4200/about)にアクセスしてaboutページを開き、お問い合わせページへリンクされているか確認しましょう。

![super rentals about page screenshot](../../images/routes-and-templates/ember-super-rentals-about.png)

<!--
Now, we'll add our corresponding link to the contact page so we can move back and forth between `about` and `contact`:
-->

お問い合わせぺージからAboutにリンクして、`about`と`contact`の間を行き来できるようにします。

```app/templates/contact.hbs{+15,+16,+17}
<div class="jumbo">
  <div class="right tomster"></div>
  <h2>Contact Us</h2>
  <p>Super Rentals Representatives would love to help you<br>choose a destination or answer
    any questions you may have.</p>
  <p>
    Super Rentals HQ
    <address>
      1212 Test Address Avenue<br>
      Testington, OR 97233
    </address>
    <a href="tel:503.555.1212">+1 (503) 555-1212</a><br>
    <a href="mailto:superrentalsrep@emberjs.com">superrentalsrep@emberjs.com</a>
  </p>
  {{#link-to "about" class="button"}}
    About
  {{/link-to}}
</div>
```

<!--
## A Rentals Route
-->

## rentalsルート(物件一覧)

<!--
In addition to our `about` and `contact` pages, we want to show a list of rentals that
our visitors can look through. So let's add a third route and call it `rentals`:
-->

`about`と`contact`ページに加えて、ユーザが物件を見渡せるように物件一覧を表示したいと思います。`rentals`という3番目のルートを追加しましょう。

```shell
ember g route rentals
```

<!--
And then let's update our new template (`/app/templates/rentals.hbs`) with some initial content.
We'll come back to this page in a bit to add in the actual rental properties.
-->

そうしたら、まずは作成されたテンプレート(`/app/templates/rentals.hbs`)を以下のように置き換えてください。
後程、実際の賃貸物件を追加しますので、またこのテンプレートに戻ってきます。

```app/templates/rentals.hbs
<div class="jumbo">
  <div class="right tomster"></div>
  <h2>Welcome!</h2>
  <p>We hope you find exactly what you're looking for in a place to stay.</p>
  {{#link-to "about" class="button"}}
    About Us
  {{/link-to}}
</div>
```

<!--
## An Index Route
-->

## インデックスルート

<!--
With our three routes in place, we are ready to add an index route, which will handle requests to the root URI (`/`) of our site.
We'd like to make the rentals page the main page of our application, and we've already created a route.
Therefore, we want our index route to simply forward to the `rentals` route we've already created.
-->

3つのルートを用意して、サイトのルートURI(`/`)へのリクエストを処理するインデックスルートを追加する準備が整いました。
物件一覧のページをアプリケーションのメインページにしましょう。既にルートは作成済みですので、インデックスルートを`rentals`ルートにリダイレクトすれば完了です。

<!--
Using the same process we did for our about and contact pages, we will first generate a new route called `index`.
-->

aboutとcontactページで行ったのと同じように、まず `index`という新しいルートを生成します。

```shell
ember g route index
```

<!--
We can see the now familiar output for the route generator:
-->

ルートジェネレータがいつも通りに出力しているのが見えるはずです。

```shell
installing route
  create app/routes/index.js
  create app/templates/index.hbs
installing route-test
  create tests/unit/routes/index-test.js
```

<!--
Unlike the other route handlers we've made so far, the `index` route is special:
it does NOT require an entry in the router's mapping.
We'll learn more about why the entry isn't required later on when we look at [nested routes](../subroutes) in Ember.
-->

これまでに作成した他のルートハンドラとは異なり、`index`ルートは特別です。ルータのマッピングにエントリを必要としません。
なぜエントリが必要とされないのかについては、この後の[ネストしたルートの作成](../subroutes)で学びます。

<!--
All we want to do when a user visits the root (`/`) URL is transition to `/rentals`.
To do this we will add code to our index route handler by implementing a route lifecycle hook,
called `beforeModel`.
-->

ユーザがルート(`/`)URLにアクセスしたときにやりたいことは、 `/rentals`へのリダイレクトです。
これを行うために、インデックスルートハンドラに`beforeModel`と呼ばれるルートライフサイクルフックを実装してコードを追加します。

<!--
Each route handler has a set of "lifecycle hooks", which are functions that are invoked at specific times during the loading of a page.
The [`beforeModel`](https://www.emberjs.com/api/ember/2.16/classes/Route/methods/beforeModel?anchor=beforeModel)
hook gets executed before the data gets fetched from the model hook, and before the page is rendered.
See [the next section](../model-hook) for an explanation of the model hook.
-->

各ルートハンドラには、「ライフサイクルフック」というものがあります。これはページのロード中に決まったタイミングで呼び出される関数です。
[`beforeModel`](https://www.emberjs.com/api/ember/2.16/classes/Route/methods/beforeModel?anchor=beforeModel)は、`model`フックでデータが取得される前、ページが描画されるより前に呼び出されます。
モデルフックの説明については、[次のセクション](../model-hook)を参照してください。

<!--
In our index route handler, we'll call the [`replaceWith`](https://www.emberjs.com/api/ember/2.16/classes/Route/methods/beforeModel?anchor=replaceWith) function.
The `replaceWith` function is similar to the route's [`transitionTo()`](https://www.emberjs.com/api/ember/2.16/classes/Route/methods/transitionTo?anchor=transitionTo) function,
the difference being that `replaceWith` will replace the current URL in the browser's history,
while `transitionTo` will add to the history.
Since we want our `rentals` route to serve as our home page, we will use the `replaceWith` function.
-->

インデックスルートハンドラでは、`replaceWith`関数を使います。
`replaceWith`関数はルートの`transitionTo`関数と似ていますが、`transitionTo`はブラウザの履歴に追加されるのに対し、`replaceWith`は履歴の現在のURLが置き換わります。
`rentals`ルートがトップページとして機能するようにしたいので、`replaceWith`関数を使用します。

<!--
In our index route handler, we add the `replaceWith` invocation to `beforeModel`.
-->

インデックスルートハンドラの`beforeModel`フック内で`replaceWith`関数を実行するように変更します。

```app/routes/index.js{+4,+5,+6}
import Route from '@ember/routing/route';

export default Route.extend({
  beforeModel() {
    this.replaceWith('rentals');
  }
});
```

<!--
Now visiting the root route at `/` will result in the `/rentals` URL loading.
-->

これで、`/`でルートルートにアクセスすると、 `/rentals` URLが読み込まれるようになりました。

<!--
## Adding a Banner with Navigation
-->

## ナビゲーション付きバナーの追加

<!--
In addition to adding individual links to each route of our app, we'd like to
add a common header across the top of our page to display our app's title and its navigation bar.
-->

アプリの各ページに各ルートへのリンクを個別に追加はせず、共通のヘッダーをページ上部に追加して、アプリのタイトルとナビゲーションバーを表示するようにしたいと思います。

<!--
To show something on every page, we can use the application template (which we edited earlier).
Let's open it again (`/app/templates/application.hbs`) and replace its contents with the following:
-->

すべてのページに何かを表示するには、アプリケーションテンプレート(上記で編集したもの)を使用できます。
もう一度`/app/templates/application.hbs`を開き、内容を次のように置き換えましょう。

```app/templates/application.hbs
<div class="container">
  <div class="menu">
    {{#link-to "index"}}
      <h1>
        <em>SuperRentals</em>
      </h1>
    {{/link-to}}
    <div class="links">
      {{#link-to "about"}}
        About
      {{/link-to}}
      {{#link-to "contact"}}
        Contact
      {{/link-to}}
    </div>
  </div>
  <div class="body">
    {{outlet}}
  </div>
</div>
```

<!--
We've seen most of this before, but the `{{outlet}}` beneath `<div class="body">` is new.
The `{{outlet}}` helper tells Ember where content for our current route (such as `about`
or `contact`) should be shown.
-->

ほとんど見たことがあるものですが、`<div class="body">`の下にある`{{outlet}}`は初めてですね。
`{{outlet}}`ヘルパーは現在のルート(`about`
または`contact`)のコンテンツを置く場所をEmberに伝えます。

訳注: outletは差込口(コンセント)という意味です。

<!--
At this point, we should be able to navigate between our `about`, `contact`, and `rentals` pages.
-->

これで`about`、`contact`、`rentals`のページ間を移動できるようになったはずです。
確認してください。

<!--
From here you can move on to the [next page](../model-hook/) or dive into testing the new functionality we just added.
-->

ここから先の部分は読み飛ばして[次のページ](../model-hook/)に行っても構いません。
読み進めれば、追加したばかりの新機能を自動テストする方法を学べます。

<!--
## Implementing Acceptance Tests
-->

## 受け入れテストの実装

<!--
Now that we have various pages in our application, let's walk through how to build tests for them.
-->

アプリケーションにいくつかページを追加したので、そのテストを構築する方法について説明します。

<!--
As mentioned earlier on the [Planning the Application page](../acceptance-test/),
an Ember acceptance test automates interacting with our app in a similar way to a visitor.
-->

前に[プランニングページ](../acceptance-test/)で説明したように、
Emberの受け入れテストは、ユーザがアプリケーションとやりとりするようなやり方でテストを自動化します。

<!--
If you open the acceptance test we created (`/tests/acceptance/list-rentals-test.js`), you'll see our
goals, which include the ability to navigate to an `about` page and a `contact` page. Let's start there.
-->

作成された受け入れテストファイル(`/tests/acceptance/list-rentals-test.js`)を開いてくと、そのページにアクセスできることがテストされているのがわかります。

<!--
First, we want to test that visiting `/` properly redirects to `/rentals`. We'll use the Ember `visit` helper
and then make sure our current URL is `/rentals` once the redirect occurs.
-->

まず、 `/`を訪問すると `/rentals`に正しくリダイレ​​クトされることをテストします。 Emberの `visit`ヘルパーを使用し、リダイレクトが発生し、現在のURLが`/rentals`であることを確認します。

```/tests/acceptance/list-rentals-test.js{+2,+3,+4,+5}
test('should show rentals as the home page', function (assert) {
  visit('/');
  andThen(function() {
    assert.equal(currentURL(), '/rentals', 'should redirect automatically');
  });
});
```

<!--
Now run the tests by typing `ember test --server` in the command line (or `ember t -s` for short).
-->

コマンドラインで `ember test --server`と打ってテストを実行します(`ember t -s`と省略可)。

<!--
Instead of 7 failures there should now be 6 (5 acceptance failures and 1 ESLint).
You can also run our specific test by selecting the entry called "Acceptance | list rentals"
in the drop down input labeled "Module" on the test UI.
-->

失敗したテストが7件から6件(受け入れテストが5、ESLintが1)になったはずです。
また、「Acceptance | list rentals」という項目を選択して、実行するテストを指定できます。
テストUIの「Module」というラベルが付いているフォームを使います。

<!--
You can also toggle "Hide passed tests" to show your passing test case along with the tests that are still
failing (because we haven't yet built them).
-->

「Hide passed tests」を切り替えて、パスしたテストケースとまだ失敗しているテストを表示することもできます（まだ作成していないため）。

![6_fail](../../images/routes-and-templates/routes-and-templates.gif)

<!--
### Ember's test helpers
-->

### Emberのテストヘルパー

<!--
Ember provides a variety of acceptance test helpers to make common tasks easier,
such as visiting routes, filling in fields, clicking on links/buttons, and waiting for pages to display.
-->

Emberは、ページのアクセス、フィールドの入力、リンク/ボタンのクリック、ページの表示の待機など、一般的なタスクを簡単にするためのさまざまな受け入れテストヘルパーを提供しています。


<!--
Some of the helpers we'll use commonly are:
-->

よく使うヘルパーをいくつか紹介します。

<!--
* [`visit`](http://emberjs.com/api/classes/Ember.Test.html#method_visit) - loads a given URL
* [`click`](http://emberjs.com/api/classes/Ember.Test.html#method_click) - pretends to be a user clicking on a specific part of the screen
* `andThen` - waits for our previous commands to run before executing our function.
  In our test below, we want to wait for our page to load after `click` is called so that we can double-check that the new page has loaded
* [`currentURL`](http://emberjs.com/api/classes/Ember.Test.html#method_currentURL) - returns the URL of the page we're currently on
-->

* [`visit`](http://emberjs.com/api/classes/Ember.Test.html#method_visit) - 指定されたURLを読み込む
* [`click`](http://emberjs.com/api/classes/Ember.Test.html#method_click) - 画面の特定の部分をクリックする
* `andThen` - 以前のコマンドが実行されるのを待ってから関数を実行する
  下記のテストでは、クリック後に次のページがロードされたことをちゃんと確認するために使っている
* [`currentURL`](http://emberjs.com/api/classes/Ember.Test.html#method_currentURL) - 現在表示されているページのURLを返す

<!--
### Test visiting our About and Contact pages
-->

### Aboutとお問い合わせページにアクセスしてテストする

<!--
Now let's add code that simulates a visitor arriving on our homepage, clicking one of our links and then visiting a new page.
-->

次に、ユーザがトップページにアクセスし、リンクをクリックし、別のページにアクセスするのをシミュレートするコードを追加します。

```/tests/acceptance/list-rentals-test.js{+2,+3,+4,+5,+6,+10,+11,+12,+13,+14}
test('should link to information about the company.', function (assert) {
  visit('/');
  click('a:contains("About")');
  andThen(function() {
    assert.equal(currentURL(), '/about', 'should navigate to about');
  });
});

test('should link to contact information', function (assert) {
  visit('/');
  click('a:contains("Contact")');
  andThen(function() {
    assert.equal(currentURL(), '/contact', 'should navigate to contact');
  });
});
```

<!--
In the tests above, we're using [`assert.equal()`](https://api.qunitjs.com/assert/equal) to check if the first and second arguments equal each other.
If they don't, our test will fail.
-->

上記のテストでは、[`assert.equal()`](https://api.qunitjs.com/assert/equal)を使って第1引数と第2引数が互いに等しいかを確認しています。
等しくなければテストは失敗します。

<!--
The third optional argument allows us to provide a nicer message which will be shown if this test fails.
-->
3番目の引数は省略可能で、テストが失敗した時のメッセージを指定します。

<!--
In our tests, we also call two helpers (`visit` and `click`) one after another. Although Ember does a number
of things when we make those calls, Ember hides those complexities by giving us these [asynchronous test helpers](../../testing/acceptance/#toc_asynchronous-helpers).
-->

テストでは、2つのヘルパー(`visit`と`click`)を呼んでいます。
Emberはこれらの呼び出しを行うときにいくつかのことを行いますが、Emberの[非同期テストヘルパー](../../testing/acceptance/#toc_asynchronous-helpers)によって、その複雑さを隠しています。

<!--
If you left `ember test` running, it should have automatically updated to show the three tests related to
navigating have now passed.
-->

`ember test`を実行したままにしておくと、自動的に更新され、ナビゲートに関連する3つのテストがパスされたことが示されます。

<!--
In the screen recording below, we run the tests, deselect "Hide passed tests", and set the module to our acceptance test,
revealing the 3 tests we got passing.
-->

以下のスクリーンレコーディングでは、テストを実行し、「Hide passed tests」の選択を解除し、モジュールを受け入れテストに設定し、合格した3つのテストを見せています。

![passing navigation tests](../../images/routes-and-templates/ember-route-tests.gif)
