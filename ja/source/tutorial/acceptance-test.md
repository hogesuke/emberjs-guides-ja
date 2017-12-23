<!--
To show the basic setup of an Ember application, we'll walk through building an app for a property rental site called Super Rentals. We'll start with a homepage, an about page and a contact page.
-->

Emberアプリケーションの基本的な仕組みを説明するために、Super Rentalsという不動産賃貸サイトのアプリを作っていきます。
トップページ、About、お問い合わせの各ページから始めていきましょう。

<!--
Here's a look at what we want to build before we get started.
-->

始める前に、どんなアプリを作るか見てみましょう。

![super rentals homepage screenshot](../../images/service/style-super-rentals-maps.png)

<!--
Let's work through what we want to do on the home page. We want our application to:
-->

どんなサイトにしたいか、アプリケーションの要件をリストアップします。

<!--
* Show rentals on the home page
* Link to information about the company
* Link to contact information
* List the available rentals
* Filter the list of rentals by city
* Show more details for a selected rental
-->

* トップページに物件を表示する
* 会社情報のリンクを置く
* お問い合わせ先のリンクを置く
* 利用可能な物件の一覧を載せる
* 市で物件を絞れるようにする
* 選択された物件の詳細情報を表示する


<!--
For the remainder of this page, we'll give you an introduction to testing in Ember and get you set up to add tests as we implement pieces of our app. On subsequent tutorial pages, the final sections of each page will be devoted to adding a test for the feature you just implemented. These sections aren't required for a working application and you may move on with the tutorial without writing them.
-->

このページの残りは、Emberでアプリの実装をする度に受け入れテストを追加していくやり方を紹介します。この先の各ページの最後の部分は、実装した機能のテストの追加に割かれています。アプリケーションの動作にテストは不要ですので、テストを書かずに先に進むこともできます。

<!--
At this point, you can continue to the [next page](../routes-and-templates/) or read more about Ember testing below.
-->

ですので、後述のEmberでのテストにさらに読むか、ここで[次のページ](../routes-and-templates/)に行っても構いません。

<!--
### Testing Our Application As We Go
-->

### テストも書く

<!--
We can represent the goals above as [Ember acceptance tests](../../testing/acceptance/). Acceptance tests interact with our app like an actual person would, but are automated, helping ensure that our app doesn't break in the future.
-->

上記の要件は、[Emberの受け入れテスト](../../testing/acceptance/)で表すことができます。
受け入れテストでは、実際の人がやるようにアプリを操作しますが、自動化されており、アプリが壊れていないことを明確にするのを助けます。

<!--
When we create a new Ember Project using Ember CLI, it uses the [`QUnit`](https://qunitjs.com/) JavaScript test framework to define and run tests.
-->
Ember CLIを使ってEmberのプロジェクトを作成すると、JavaScriptテストフレームワークの[`QUnit`](https://qunitjs.com/)がテストの定義と実行に使われます。

<!--
We'll start by using Ember CLI to generate a new acceptance test:
-->

受け入れテストの作成からやってみましょう。

```shell
ember g acceptance-test list-rentals
```

<!--
The command will generate the following output, showing that it created a single file called `tests/acceptance/list-rentals-test.js`.
-->

コマンドを実行すると、以下ように出力されるはずです。
`tests/acceptance/list-rentals-test.js` という1つのファイルが作成されのを表示しています。


```shell
installing acceptance-test
  create tests/acceptance/list-rentals-test.js
```

<!--
Opening that file will reveal some initial code that will try to go to the `list-rentals` route and verify that the route is loaded. The initial code is there to help us build our first acceptance test.
-->

作成されたファイルを開くと、`list-rentals`ルートにアクセスし、そのルートが読み込まれていることを検証してるコードがあるのがわかります。
そのコードは、初めて受け入れテストを書く私たちを助けるためにあります。

<!--
Since we haven't added any functionality to our application yet, we'll use this first test to get started on running tests in our app.
-->

まだアプリケーションには何も機能を追加していませんし、これからアプリのテストをしてくために、このテストを取り入れましょう。

<!--
To do that, replace occurrences of `/list-rentals` in the generated test with `/`. The test will start our app at the base url, `http://localhost:4200/`, and then do a basic check that the page has finished loading and that the url is what we want it to be.
-->

そのために、生成されたコードの`/list-rentals`を`/`に置き換えてください。
以下のテストは、`http://localhost:4200/`でアプリにアクセスし、ページの読み込みが完了し、意図したURLにいるかどうか確認しています。

```/tests/acceptance/list-rentals-test.js{-6,+7,-8,+9,-12,+13}
import { test } from 'qunit';
import moduleForAcceptance from 'super-rentals/tests/helpers/module-for-acceptance';

moduleForAcceptance('Acceptance | list-rentals');

test('visiting /list-rentals', function(assert) {
test('visiting /', function(assert) {
  visit('/list-rentals');
  visit('/');

  andThen(function() {
    assert.equal(currentURL(), '/list-rentals');
    assert.equal(currentURL(), '/');
  });
});
```

<!--
A few of things to note in this simple test:
-->

このシンプルなテストで注意すべきことは、

<!--
* Acceptance tests are setup by calling the function `moduleForAcceptance`. This function ensures that your Ember application is started and shut down between each test.
* QUnit passes in an object called an [`assert`](https://api.qunitjs.com/assert/) to each test function. An `assert` has functions, such as `equal()`, that allow your test to check for conditions within the test environment. A test must have one passing assert to be successful.
* Ember acceptance tests use a set of test helper functions, such as the `visit`, `andThen`, and `currentURL` functions used above. We'll discuss those functions in more detail later in the tutorial.
-->

* 受け入れテストは`moduleForAcceptance`関数を呼んでセットアップされる。この関数はEmberアプリケーションが書くテストの合間に起動と終了することを確実にする。
* QUnitは各テスト関数に[`assert`](https://api.qunitjs.com/assert/)というオブジェクトを渡す。`assert`は`equal()`などの関数を持ち、テスト内で状態を調べることができる。
* Emberの受け入れテストでは`visit`、`andThen`、`currentURL`といったテストヘルパーを使う。テストヘルパーの詳細はチュートリアル内に後述する。

<!--
Now run your test suite with the CLI command, `ember test --server`.
-->

では、テストをCLIで実行しましょう。`ember test --server`

<!--
By default, when you run `ember test --server`, Ember CLI runs the [Testem test runner](https://github.com/testem/testem), which runs Qunit in Chrome and [PhantomJS](http://phantomjs.org/).
-->

デフォルトで、`ember test --server`を実行すると、Ember [Testemテストランナー](https://github.com/testem/testem)を実行し、ChromeがQUnitを実行します。

<!--
Our launched Chrome web browser now shows 10 successful tests. If you toggle the box labeled "Hide passed tests", you should see our successful acceptance test, along with 9 passing ESLint tests. Ember tests each file you create for syntax issues (known as "linting") using [ESLint](http://eslint.org/).
-->

起動されたChromeは10件テストがパスしたのを表示しています。
"Hide passed tests"というラベルにチェックを外すと、受け入れテストが1件、ESLintが9件パスしたのが表示されます。Emberは[ESLint](http://eslint.org/)で、構文の確認(lint)を実行します。

![Initial Tests Screenshot](../../images/acceptance-test/initial-tests.png)

<!--
### Adding Your Application Goals as Acceptance Tests
-->

### アプリケーションの要件を受け入れテストとして追加する


<!--
As mentioned before, our initial test just made sure everything was running properly. Now let's replace that test with the list of tasks we want our app to handle (described up above).
-->

前述した通り、テストで全部きちんと動いているか確実にすることができました。
それでは、上記のアプリの要件に合わせたテストに置き換えましょう。

```/tests/acceptance/list-rentals-test.js{+6,+7,+8,+9,+10,+11,+12,+13,+14,+15,+16,+17,+18,+19,+20,+21,+22,-23,-24,-25,-26,-27,-28,-29}
import { test } from 'qunit';
import moduleForAcceptance from 'super-rentals/tests/helpers/module-for-acceptance';

moduleForAcceptance('Acceptance | list-rentals');

test('should show rentals as the home page', function (assert) {
});

test('should link to information about the company.', function (assert) {
});

test('should link to contact information.', function (assert) {
});

test('should list available rentals.', function (assert) {
});

test('should filter the list of rentals by city.', function (assert) {
});

test('should show details for a selected rental', function (assert) {
});
test('visiting /', function(assert) {
  visit('/');

  andThen(function() {
    assert.equal(currentURL(), '/');
  });
});
```

<!--
Running `ember test --server` will now show 7 failing tests (out of 15). Each of the 6 tests we setup above will fail, plus one ESLint test will fail saying, `assert is defined but never used`. The tests above fail because QUnit requires at least one check for a specific condition (known as an `assert`).
-->

`ember test --server`を実行すると7件の失敗が表示されるはずです。6件は、先ほど置き換えたテストで、1件は`assert`は定義されているが一度も使われていないというESLintの報告です。上記のテストが失敗するのはQUnitが少なくとも1つの`assert`を要求するからです。

<!--
As we continue through this tutorial, we'll use these acceptance tests as our checklist. Once all the tests are passing, we'll have accomplished our high level goals.
-->

このチュートリアルを続けていく中で、これらの受け入れテストをチェック項目として使います。
全てのテストがパスしていれば、高い目標を達成したことになります。

![Initial Tests Screenshot](../../images/acceptance-test/acceptance-test.png)
