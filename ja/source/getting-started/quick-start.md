<!--
This guide will teach you how to build a simple app using Ember from scratch.
-->

このガイドでは、Emberを使用したシンプルなアプリケーションを一から作成する方法を教えます。

<!--
We'll cover these steps:
-->

以下のステップが対象です。

<!--
1. Installing Ember.
2. Creating a new application.
3. Defining a route.
4. Writing a UI component.
5. Building your app to be deployed to production.
-->

1. Emberをインストールする
2. アプリケーションを作成する
3. ルートを定義する
4. UIコンポーネントを書く
5. リリース用にビルドする

<!--
## Install Ember
-->

## Emberのインストール

<!--
You can install Ember with a single command using npm,
the Node.js package manager.
Type this into your terminal:
-->

Node.jsのパッケージマネージャーのnpmを使ってEmberをインストールできます。
ターミナルに以下のコマンドを入力してください。

```sh
npm install -g ember-cli
```

<!--
Don't have npm? [Learn how to install Node.js and npm here](https://docs.npmjs.com/getting-started/installing-node).
For a full list of dependencies necessary for an Ember CLI project,
consult our [Installing Ember](../../getting-started/) guide.
-->

npmが入っていない場合は、 [Installing Node.js and updating npm](https://docs.npmjs.com/getting-started/installing-node)(英語)を参照してください。
Ember CLIのプロジェクトで必要な物のリストは、ガイド内の[Emberのインストール](../../getting-started/)を参照してください。


<!--
## Create a New Application
-->

## アプリケーションを作成する

<!--
Once you've installed Ember CLI via npm,
you will have access to a new `ember` command in your terminal.
You can use the `ember new` command to create a new application.
-->

Ember CLIをnpmでインストールすると、ターミナル上で`ember`コマンドを実行できるようになります。
アプリケーションの作成は`ember new`を使います。

```sh
ember new ember-quickstart
```

<!--
This one command will create a new directory called `ember-quickstart` and set up a new Ember application inside of it.
Out of the box, your application will include:
-->

このコマンドは`ember-quickstart`と名付けられた新規ディレクトリを作成し、
その中に新しいEmberアプリケーションをセットアップします。
アプリケーションには以下のものが最初から含まれています。

<!--
* A development server.
* Template compilation.
* JavaScript and CSS minification.
* ES2015 features via Babel.
-->

* 開発用サーバー
* テンプレートのコンパイル機能
* JavaScriptとCSSの縮小機能
* BabelによるES2015の機能

<!--
By providing everything you need to build production-ready web applications in an integrated package,
Ember makes starting new projects a breeze.
-->

プロダクト向けのWebアプリケーションのビルドできるように、
必要なもの全てを1つのパッケージで入ることで、
Emberでは新しいプロジェクトを楽に始めることができます。

<!--
Let's make sure everything is working properly.
`cd` into the application directory `ember-quickstart` and start the development server by typing:
-->

それでは、全部きちんと動くか確認しましょう。
`ember-quick-start`ディレクトリに`cd`で移動し、開発用サーバを起動します。
以下のコマンドをターミナルに入力してください。

```sh
cd ember-quickstart
ember serve
```

<!--
After a few seconds, you should see output that looks like this:
-->

数秒後、以下の出力が表示されるはずです。


```text
Livereload server on http://localhost:7020
Serving on http://localhost:4200/
```

<!--
(To stop the server at any time, type Ctrl-C in your terminal.)
-->

(サーバを止めるには、Ctrl-Cを入力してください。)

<!--
Open [`http://localhost:4200`](http://localhost:4200) in your browser of choice.
You should see an Ember welcome page and not much else.
Congratulations! You just created and booted your first Ember app.
-->

[`http://localhost:4200`](http://localhost:4200) をブラウザで開いてください。
Emberのウェルカムページが表示されるはずです。
おめでとうございます！初めてのEmberアプリの作成と起動ができました。

<!--
We will start by editing the `application` template.
This template is always on screen while the user has your application loaded.
In your editor, open `app/templates/application.hbs` and change it to the following:
-->

`application`テンプレートの編集から始めていきましょう。
このテンプレートはユーザがアプリケーションをロードしてからずっと表示されます。
エディタで、`app/templates/application.hbs`を開き以下のように変更してください。


```app/templates/application.hbs
<h1>PeopleTracker</h1>

{{outlet}}
```

<!--
Ember detects the changed file and automatically reloads the page for you in the background.
You should see that the welcome page has been replaced by "PeopleTracker".
You also added an `{{outlet}}` to this page,
which means that any nested route will be rendered in that place.
-->

Emberはファイルの変更を自動で検出し、バックグランドでページをリロードします。
ウェルカムページが"PeopleTracker"に置き換わったのが見えるはずです。
このページに`{{outlet}}`も合わせて追加しましたので、
ネストしたルートはそこに表示されることになります。

<!--
## Define a Route
-->

## ルートを定義する

<!--
Let's build an application that shows a list of scientists.
To do that, the first step is to create a route.
For now, you can think of routes as being the different pages that make up your application.
-->

科学者の一覧を表示するアプリケーションを作成しましょう。
それにはまずルートの作成から始めます。
とりあえず、ルートというのはアプリケーション内の違うページのことだと考えておけば大丈夫です。

<!--
Ember comes with _generators_ that automate the boilerplate code for common tasks.
To generate a route, type this in a new terminal window in your `ember-quickstart` directory:
-->

Emberにはジェネレータがあり、雛形は自動で作ることができます。
ルートを作成するために、`ember-quickstart`ディレクトリで以下のコマンドを入力してください。

```sh
ember generate route scientists
```

<!--
You'll see output like this:
-->

以下ようなの出力が表示されます。

```text
installing route
  create app/routes/scientists.js
  create app/templates/scientists.hbs
updating router
  add route scientists
installing route-test
  create tests/unit/routes/scientists-test.js
```
<!--
That is Ember telling you that it has created:
-->

これはEmberが作成したものを表示しています。

<!--
1. A template to be displayed when the user visits `/scientists`.
2. A `Route` object that fetches the model used by that template.
3. An entry in the application's router (located in `app/router.js`).
4. A unit test for this route.
-->

1. ユーザが`/scientists`にアクセスした時に表示されるテンプレート
2. 1.のテンプレートで使用されるデータ(モデル)を取得するルート
3. アプリケーションルーターに`/scientists`パスを追加(`app/router.js`に配置されている).
4. 2.で作成されたルートのユニットテスト

<!--
Open the newly-created template in `app/templates/scientists.hbs` and add the following HTML:
-->

作成された`app/templates/scientists.hbs`を開き、以下のHTMLを追加してください。

```app/templates/scientists.hbs
<h2>List of Scientists</h2>
```

<!--
In your browser, open [`http://localhost:4200/scientists`](http://localhost:4200/scientists).
You should see the `<h2>` you put in the `scientists.hbs` template,
right below the `<h1>` from our `application.hbs` template.
-->

ブラウザで[`http://localhost:4200/scientists`](http://localhost:4200/scientists)を開いてください。
`application.hbs`の`<h1>`の真下に、`scientists.hbs`に追加した`<h2>`が見えるはずです。

<!--
Now that we've got the `scientists` template rendering,
let's give it some data to render.
We do that by specifying a _model_ for that route,
and we can specify a model by editing `app/routes/scientists.js`.
-->

`scientists` のテンプレートが描画されましたので、いくつかデータを描画してましょう。
データを表示するには、そのルートの _model_ を指定します。 modelの指定は、
`app/routes/scientists.js` を変更します。

<!--
We'll take the code created for us by the generator and add a `model()` method to the `Route`:
-->

ジェネレータによって作成されたコードを残したまま、`model()`メソッドを`Route`に追加してください。

```app/routes/scientists.js{+4,+5,+6}
import Route from '@ember/routing/route';

export default Route.extend({
  model() {
    return ['Marie Curie', 'Mae Jemison', 'Albert Hofmann'];
  }
});
```

This code example uses the latest features in JavaScript,
some of which you may not be familiar with.
Learn more with this [overview of the newest JavaScript features](https://ponyfoo.com/articles/es6).

<!--
In a route's `model()` method, you return whatever data you want to make available to the template.
If you need to fetch data asynchronously,
the `model()` method supports any library that uses [JavaScript Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise).
-->

ルートの`model()`メソッドでは、テンプレートで使いたいものをreturnしてください。
非同期にデータを取得する場合でも、`model()`は[JavaScript Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)を使用しているライブラリをサポートしています。

<!--
Now let's tell Ember how to turn that array of strings into HTML.
Open the `scientists` template and add the following code to loop through the array and print it:
-->

では、モデルが返す文字列の配列をHTMLで表示しましょう。
`scientists`のテンプレートをエディタで開き、以下のコードを追加し、配列を回して表示してください。

```app/templates/scientists.hbs{+3,+4,+5,+6,+7}
<h2>List of Scientists</h2>

<ul>
  {{#each model as |scientist|}}
    <li>{{scientist}}</li>
  {{/each}}
</ul>
```

<!--
Here, we use the `each` helper to loop over each item in the array we provided from the `model()` hook and print it inside an `<li>` element.
-->

ここでは、`model()`フックから渡された配列の全要素を回すために`each`を使い、`<li>`要素内に表示しています。

<!--
## Create a UI Component
-->

## UIコンポーネントを書く

<!--
As your application grows, you will notice you are sharing UI elements between multiple pages,
or using them multiple times on the same page.
Ember makes it easy to refactor your templates into reusable components.
-->

アプリケーションが大きくなるにつれ、複数のページ間で同じUIを使っていたり、または同じページ内で何度も表示していたりすることに気がつくことがあります。
Emberではテンプレートを再利用可能なコンポーネントに簡単に書き直せます。

<!--
Let's create a `people-list` component that we can use in multiple places to show a list of people.
-->

人物一覧内 で繰り返す使える`people-list`コンポネーントを作成してみましょう。

<!--
As usual, there's a generator that makes this easy for us.
Make a new component by typing:
-->

いつも通り、ジェネレータがありますので、以下を入力してください。

```sh
ember generate component people-list
```

<!--
Copy and paste the `scientists` template into the `people-list` component's template and edit it to look as follows:
-->

`scientists`テンプレート内のコードを`people-list`コンポーネントのテンプレートにコピペし、以下のように変更してください。

```app/templates/components/people-list.hbs
<h2>{{title}}</h2>

<ul>
  {{#each people as |person|}}
    <li>{{person}}</li>
  {{/each}}
</ul>
```

<!--
Note that we've changed the title from a hard-coded string ("List of Scientists") to a dynamic property (`{{title}}`).
We've also renamed `scientist` to the more-generic `person`,
decreasing the coupling of our component to where it's used.
-->

ハードコードされていたタイトル("List of Sceintists")が動的プロパティ(`{{title}}`)に置き換わっているのに注意してください。

<!--
Save this template and switch back to the `scientists` template.
Replace all our old code with our new componentized version.
Components look like HTML tags but instead of using angle brackets (`<tag>`) they use double curly braces (`{{component}}`).
-->

テンプレートを保存し、 `scientists` テンプレートに戻ります。
前のコードをコンポーネントを使ったコードに置き換えます。
コンポーネオントはHTMLタグのように見えますが、山括弧(`<tag>`)の代わりに2回波括弧(`{{component}}`)を使います。

<!--
We're going to tell our component:
-->

コンポーネントに以下のように指定します。

<!--
1. What title to use, via the `title` attribute.
2. What array of people to use, via the `people` attribute. We'll
   provide this route's `model` as the list of people.
-->

1. `title`属性で使用するtitleを渡す
2. `people`属性で人の配列を渡す (このルートの`model`で人の配列を返している)

```app/templates/scientists.hbs{-1,-2,-3,-4,-5,-6,-7,+8}
<h2>List of Scientists</h2>

<ul>
  {{#each model as |scientist|}}
    <li>{{scientist}}</li>
  {{/each}}
</ul>
{{people-list title="List of Scientists" people=model}}
```

<!--
Go back to your browser and you should see that the UI looks identical.
The only difference is that now we've componentized our list into a version that's more reusable and more maintainable.
-->

ブラウザに戻ると、同じ見た目のUIが表示されているはずです。
唯一違うのは、一覧をコンポーネントに置き換え、より再利用可能でメンテナンスしやすいコードに置き換わったことです。

<!--
You can see this in action if you create a new route that shows a different list of people.
As an exercise for the reader,
you may try to create a `programmers` route that shows a list of famous programmers.
By re-using the `people-list` component, you can do it in almost no code at all.
-->

新しいルートを作成し違う人物一覧を表示すれば実際に分かるでしょう。
練習として、 有名なプログラマーの一覧を表示する `programmers` ルートを作成してみるのもいいですね。

<!--
## Click Events
-->

## クリックイベント

<!--
So far, your application is listing data,
but there is no way for the user to interact with the information.
In web applications you often want to listen for user events like clicks or hovers.
Ember makes this easy to do.
First add an `action` helper to the `li` in your `people-list` component.
-->

ここまでデータの一覧表示をやりましたが、まだユーザがその情報を操作したりできません。
Webアプリケーションでは、クリックやホバーといったイベントを使いたいでしょう。
Emberでは簡単にそれをすることができます。
まず始めに、`people-list`コンポーネント内の`li`に`action`ヘルパーを追加します。

```app/templates/components/people-list.hbs{-5,+6}
<h2>{{title}}</h2>

<ul>
  {{#each people as |person|}}
    <li>{{person}}</li>
    <li {{action "showPerson" person}}>{{person}}</li>
  {{/each}}
</ul>
```

<!--
The `action` helper allows you to add event listeners to elements and call named functions.
By default, the `action` helper adds a `click` event listener,
but it can be used to listen for any element event.
Now, when the `li` element is clicked a `showPerson` function will be called from the `actions` object in the `people-list` component.
Think of this like calling `this.actions.showPerson(person)` from our template.
-->

`action`ヘルパーは要素にイベントリスナーを追加し名前付き関数を呼ぶようにしてくれます。
デフォルトで、`action`ヘルパーは`click`イベントリスナーを追加しますが、
どの要素でも使えるわけではありません。
さて、これで`li`要素をクリックすると、`people-list`コンポーネントの`actions`オブジェクト内の`showPerson`関数が呼ばれるようになりました。
ここでは `this.actions.showPerson(person)` がテンプレートから呼ばれていると考えておきましょう。

<!--
To handle this function call you need to modify the `people-list` component file to add the function to be called.
In the component, add an `actions` object with a `showPerson` function that alerts the first argument.
-->

この関数コールをハンドルするためには、`people-list`コンポーネントに呼ばれる関数を追加する必要があります。
コンポーネント内に、`actions`をオブジェクトとして追加し、第一引数を`alert`する`showPerson`関数を入れましょう。


```app/components/people-list.js{+4,+5,+6,+7,+8}
import Component from '@ember/component';

export default Component.extend({
  actions: {
    showPerson(person) {
      alert(person);
    }
  }
});
```

<!--
Now in the browser when a scientist's name is clicked,
this function is called and the person's name is alerted.
-->

ブラウザ上で科学者の名前がクリックされると、この関数が呼ばれ、その人物の名前が`alert`されるようになりました。

<!--
## Building For Production
-->

## リリース用にビルドする

<!--
Now that we've written our application and verified that it works in development,
it's time to get it ready to deploy to our users.
-->

さて、アプリケーションを開発環境上で書いて検証してきましたが、デプロイできるようにしてみましょう。

<!--
To do so, run the following command:
-->

そうするには、以下のコマンドを実行します。

```sh
ember build --env production
```

<!--
The `build` command packages up all of the assets that make up your
application&mdash;JavaScript, templates, CSS, web fonts, images, and
more.
-->

`build` コマンドは、アプリケーションを構成する全てのアセットをまとめます。&mdash; JavaScript、テンプレート、CSS、Webフォント、画像など

<!--
In this case, we told Ember to build for the production environment via the `--env` flag.
This creates an optimized bundle that's ready to upload to your web host.
Once the build finishes,
you'll find all of the concatenated and minified assets in your application's `dist/` directory.
-->

今回の場合、`--env`フラグでプロダクション環境にビルドしています。
プロダクションビルドでは、リリース可能で最適化されたものが作成されます。
ビルドが終わると、連結され縮小されたアセットを`dist`ディレクトリで見ることができます。

<!--
The Ember community values collaboration and building common tools that everyone relies on.
If you're interested in deploying your app to production in a fast and reliable way,
check out the [Ember CLI Deploy](http://ember-cli-deploy.com/) addon.
-->

The Ember community values collaboration and building common tools that everyone relies on. If you're interested in deploying your app to production in a fast and reliable way, check out the Ember CLI Deploy addon.


Emberコミュニティは協力や皆が頼りにするツールを作ることを大事にしています。
速く、信頼できる方法でプロダクションへデプロイすることに興味がある場合は、[Ember CLI Deploy](http://ember-cli-deploy.com/)アドオンをチェックしてみてください。

<!--
If you deploy your application to an Apache web server, first create a new virtual host for the application.
To make sure all routes are handled by index.html,
add the following directive to the application's virtual host configuration:
-->

Apacheを使用している場合は、新しくバーチャルホストを作成してください。
全てのルートが `index.html` に向けられるように、以下のディレクティブをバーチャルホストの設定に追加してください。

```apache
FallbackResource index.html
```
