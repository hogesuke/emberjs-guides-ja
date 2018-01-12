<!--
Welcome to the Ember Tutorial!
This tutorial is meant to introduce basic Ember concepts while creating a professional looking application.
If you get stuck at any point during the tutorial, feel free to download [https://github.com/ember-learn/super-rentals](https://github.com/ember-learn/super-rentals) for a working example of the completed app.
-->

Emberチュートリアルへようこそ！
このチュートリアルでは、プロレベルのアプリケーションを作成しながらEmberの基本的なコンセプトを紹介していきます。
もしチュートリアルを進めていく中で行き詰まったら、いつでも[https://github.com/ember-learn/super-rentals](https://github.com/ember-learn/super-rentals)から完成済みの動くサンプルをダウンロードしてみてください。

<!--
You can install the latest version of `ember-cli` by following the [Quick Start](../../getting-started/quick-start/#toc_install-ember) guide "Installing Ember" section.
-->

最新の`ember-cli`のインストールの仕方は、[クイックスタート](../../getting-started/quick-start/#toc_Emberのインストール)のEmberのインストールの項に記載されてます。

<!--
Ember CLI, Ember's command line interface, provides a standard project
structure, a set of development tools, and an addon system.
This allows Ember developers to focus on building apps rather
than building the support structures that make them run.
From your command line, a quick `ember --help` shows
the commands Ember CLI provides. For more information on a specific command,
type `ember help <command-name>`.
-->

Ember CLI(Emberのコマンドラインインターフェース)は、標準化されたプロジェクト構造や開発用ツール群やアドオン機能を提供します。
それにより開発者は、構造やビルドよりも、アプリの開発に集中できます。
コマンドラインで`ember --help`を入力すれば、Ember CLIが提供する機能を見ることができます。
さらに各コマンドの詳細を知りたければ、`ember help <command-name>`と入力してください。

<!--
## Creating a New App
-->

## アプリを作成する

<!--
To create a new project using Ember CLI, use the `new` command. In preparation
for the tutorial in the next section, you can make an app called `super-rentals`.
-->

Ember CLIで新しいプロジェクトを作る時は`new`コマンドを使います。
次のセクションの準備で、`super-rentals`というアプリを作ってみましょう。


```shell
ember new super-rentals
```

<!--
A new project will be created inside your current directory. You can now go to
your `super-rentals` project directory and start working on it.
-->

プロジェクトはカレントディレクトリ内に作成された新しいディレクトリ内にあります。
`super-rentals`に移動して開発を始めましょう。

```shell
cd super-rentals
```

<!--
## Directory Structure
-->

## ディレクトリ構成

<!--
The `new` command generates a project structure with the following files and
directories:
-->

`new`コマンドは以下のディレクトリとファイル構成を持つプロジェクトを作成します。

```text
|--app
|--config
|--node_modules
|--public
|--tests
|--vendor

<other files>

ember-cli-build.js
package.json
README.md
testem.js
```

<!--
Let's take a look at the folders and files Ember CLI generates.
-->

Ember CLIが作成したフォルダとファイルを見ていきましょう。

<!--
**app**: This is where folders and files for models, components, routes,
templates and styles are stored. The majority of your coding on an Ember
project happens in this folder.
-->

**app**: モデル、コンポーネント、ルート、テンプレート、CSSのフォルダとファイルを置くところです。Emberアプリのコーディングは、ほとんどがこのフォルダでやることになります。

<!--
**config**: The config directory contains the `environment.js` where you can
configure settings for your app.
-->

**config**: configディレクトリはアプリケーションの設定ができる`environment.js`があります。

<!--
**node_modules / package.json**: This directory and file are from npm.
npm is the package manager for Node.js. Ember is built with Node and uses a
variety of Node.js modules for operation. The `package.json` file maintains the
list of current npm dependencies for the app.  Any Ember CLI
addons you install will also show up here. Packages listed in `package.json`
are installed in the node_modules directory.
-->

**node_modules / package.json**: npmに関連するものです。npmはNode.jsのパッケージマネージャです。EmberはNodeで作られており、Nodeのあらゆる機能を使っています。`package.json`はアプリの依存関係を管理したリストです。Ember CLIアドオンをインストールすると`package.json`に追加され、`node_modules`配下にインストールされます。

<!--
**public**: This directory contains assets such as images and fonts.
-->

**public**: 画像やフォントなどを置くディレクトリです。

<!--
**vendor**: This directory is where front-end dependencies (such as JavaScript
or CSS) that are not managed by NPM go.
-->

**vendor**: NPMによって管理さていないフロントエンドの依存関係(JavaScriptやCSSなど)を置くディレクトリです。

<!--
**tests / testem.js**: Automated tests for our app go in the `tests` folder,
and Ember CLI's test runner **testem** is configured in `testem.js`.
-->

**tests / testem.js**: アプリの自動テストは`tests`フォルダに起きます。Ember CLIのテストランナーの**testem**は`testem.js`で設定されています。

<!--
**ember-cli-build.js**: This file describes how Ember CLI should build our app.
-->

**ember-cli-build.js**: このファイルにはEmber CLIがどのようにアプリをビルドするかを記述します。

<!--
## ES6 Modules
-->

## ES6モジュール

<!--
If you take a look at `app/router.js`, you'll notice some syntax that may be
unfamiliar to you.
-->

`app/router.js`を見ると、馴染みのない構文に気がつくかもしれません。


```app/router.js
import EmberRouter from '@ember/routing/router';
import config from './config/environment';

const Router = EmberRouter.extend({
  location: config.locationType,
  rootURL: config.rootURL
});

Router.map(function() {
});

export default Router;
```
<!--
Ember CLI uses ECMAScript 2015 (ES2015 for short or previously known as ES6) modules to organize application
code.
For example, the line `import EmberRouter from '@ember/routing/router';` gives us access to
Ember's Router class as the variable `EmberRouter`. And the `import config from
'./config/environment';` line gives us access to our app's configuration data
as the variable `config`. `const` is a way to declare a read-only variable to make
sure it is not accidentally reassigned elsewhere. At the end of the file,
`export default Router;` makes the `Router` variable defined in this file available 
to other parts of the app.
-->

Ember CLIはアプリ内のコードを組織するのに、ECMAScript 2015(ES2015、ES6)モジュールを使用しています。
例えば、`import EmberRouter from '@ember/routing/router';`の行は、EmberのRouterクラスをEmberRouterとしてアクセスできるようします。そして、`import config from './config/environment';`の行は、configという変数でアプリの設定データにアクセスできるようにしています。
`const`はリードオンリーな変数を宣言し、他のところで思いがけない再代入がないことを明確にしています。
ファイルの最後部の`export default Router;`は、このファイル内で定義されている`Router`変数を、アプリの他の部分から利用できるようにしています。

<!--
## The Development Server
-->

## 開発サーバ

<!--
Once we have a new project in place, we can confirm everything is working by
starting the Ember development server:
-->

新しいプロジェクトが設置されましたので、全部きちんと動くか、開発サーバを起動して確かめましょう。

```shell
ember serve
```

or, for short:

```shell
ember s
```

<!--
If we navigate to [`http://localhost:4200`](http://localhost:4200), we'll see the default welcome screen.
When we edit the `app/templates/application.hbs` file, we'll replace that content with our own.
-->

[`http://localhost:4200`](http://localhost:4200)にアクセスすると、デフォルトのウェルカムページが見れるはずです。
`app/templates/application.hbs`ファイルを変更すると、変更した内容に変わるはずです。


![default welcome screen](../../images/ember-cli/default-welcome-page.png)

<!--
The first thing we want to do in our new project is to remove the welcome screen.
We do this by simply opening up the application template file located at `app/templates/application.hbs`.
-->

まず初めに、このウェルカムページを消しましょう。
`app/templates/application.hbs`にあるアプリケーションテンプレートを開いてください。

<!--
Once open, remove the component labeled `{{welcome-page}}`.
The application should now be a completely blank canvas to build our application on.
-->

開いたら、`{{welcome-page}}`を消します。
ブラウザに完全に空白なページが表示されているか確認してください。

```app/templates/application.hbs{-1,-2,-3}
{{!-- The following component displays Ember's default welcome message. --}}
{{welcome-page}}
{{!-- Feel free to remove this! --}}

{{outlet}}

```
