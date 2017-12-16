<!--
Getting started with Ember is easy. Ember projects are created and managed
through our command line build tool Ember CLI.
This tool provides:
-->

Emberを始めるのは簡単です。
EmberのプロジェクトはコマンドラインビルドツールであるEmber CLIによって作成され管理されます。
Ember CLIは以下のものを提供します。

<!--
* Modern application asset management (including concatenation, minification, and versioning).
* Generators to help create components, routes, and more.
* A conventional project layout, making existing Ember applications easy to approach.
* Support for ES2015/ES6 JavaScript via the [Babel](https://babeljs.io/learn-es2015/) project. This includes support for [JavaScript modules](http://exploringjs.com/es6/ch_modules.html), which are used throughout this guide.
* A complete [QUnit](https://qunitjs.com/) test harness.
* The ability to consume a growing ecosystem of [Ember Addons](https://emberobserver.com/).
-->

* モダンなアプリケーションアセット管理(結合、縮小、バージョニング)
* コンポーネントやルートなどの作成を手助けするジェネレータ
* [Babel](https://babeljs.io/learn-es2015/)を使ったES2015とES6のサポートと、本ガイド内で使われている[JavaScript modules](http://exploringjs.com/es6/ch_modules.html)
* [QUnit](https://qunitjs.com/)を完全に活用するためテストツール群
* [Emberアドオン](https://emberobserver.com/)のサポート

<!--
## Dependencies
-->

<h2 id="toc_dependencies" class="anchorable-toc">必要なもの</h2>

<!--
### Git
-->

<h3 id="toc_git" class="anchorable-toc">Git</h3>

<!--
Ember requires Git to manage many of its dependencies. Git comes with Mac OS
X and most Linux distributions. Windows users can
download and run [this Git installer](http://git-scm.com/download/win).
-->

Emberはたくさんの依存関係を管理するためにGitを必要とします。
GitはMacとほとんどのLinuxディストリビューションにはインストールされています。
Windowsユーザは[こちらからGitインストーラ](http://git-scm.com/download/win)をダウンロードできます。


<!--
### Node.js and npm
-->

<h3 id="toc_nodejs-and-npm" class="anchorable-toc">Git</h3>

<!--
Ember CLI is built with JavaScript, and requires the most recent LTS version of the [Node.js](https://nodejs.org/)
runtime. It also requires dependencies fetched via [npm](https://www.npmjs.com/). npm is packaged with Node.js, so if your computer has Node.js
installed you are ready to go.
-->

Ember CLIはJavaScriptで作られており、最新の[Node.js](https://nodejs.org/)のLTSを必要とします。
さらに依存しているものを[npm](https://www.npmjs.com/)で取得します。
npmはNode.jsに同封されているため、Node.jsが入っていれば準備完了です。


<!--
If you're not sure whether you have Node.js or the right version, run this on your
command line:
-->

もし、Node.jsがインストールされているか、正しいバージョンなのかが分からない場合は、以下のコマンドを実行して確認してください。

```bash
node --version
npm --version
```

<!--
If you get a *"command not found"* error or an outdated version for Node:
-->

もし、コマンドが見つからないといったエラーが発生したり、古いバージョンの場合は、

<!--
* Windows or Mac users can download and run [this Node.js installer](http://nodejs.org/en/download/).
* Mac users often prefer to install Node using [Homebrew](http://brew.sh/). After
installing Homebrew, run `brew install node` to install Node.js. Alternatively, installer packages are available directly
from [Node.js](https://nodejs.org/en/download/).
* Linux users can use [this guide for Node.js installation on Linux](https://nodejs.org/en/download/package-manager/).
-->

* WindowsかMacの方は、[Node.jsインストーラ](http://nodejs.org/en/download/)をダウンロードして実行してください
* Macユーザは[Homebrew](http://brew.sh/)を使ってNode.jsをインストールする人も多いです。Homebrewインストール後に`brew install node` を実行し、Node.jsをインストールしてください。その他の手段では、[Node.jsのサイトのダウンロードページ](https://nodejs.org/ja/download/)から直接インストーラパッケージがあります。
* Linuxユーザは[Node.jsのサイトのダウンロードページ](https://nodejs.org/ja/download/)を参照してください。

<!--
If you get an outdated version of npm, run `npm install -g npm`.
-->

もし古いバージョンのnpmだった場合は、`npm install -g npm`を実行してください。

<!--
### Watchman (optional)
-->

<h3 id="toc_watchman-optional" class="anchorable-toc">Watchman (任意)</h3>

<!--
On Mac and Linux, you can improve file watching performance by installing [Watchman](https://facebook.github.io/watchman/docs/install.html).
-->

MacとLinuxでは、[Watchman](https://facebook.github.io/watchman/docs/install.html)をインストールすることでファイルの監視のパフォーマンスを改善することができます。

<!--
## Installation
-->

<h2 id="toc_installation" class="anchorable-toc">インストール</h2>

<!--
Install Ember using npm:
-->
npmを使ってEmberをインストールします。

```bash
npm install -g ember-cli
```

<!--
To verify that your installation was successful, run:
-->

インストールが成功したか検証するには、以下のコマンドを実行してください。

```bash
ember -v
```

<!--
If a version number is shown, you're ready to go.
-->

バージョンが表示されていれば準備完了です。
