<!--
Welcome to the Ember.js Guides! This documentation will take you from
total beginner to Ember expert.
-->

Ember.jsのガイドへようこそ！このドキュメントはあなたを全くの初心者からEmberのエキスパートへと導くでしょう。

<!--
## What is Ember?
-->

<h2 id="toc_what-is-ember" class="anchorable-toc">Emberとは</h2>

<!--
Ember is a JavaScript front-end framework designed to help you build websites with rich and complex user interactions.
It does so by providing developers both with many features that are essential to manage complexity in modern web applications,
as well as an integrated development toolkit that enables rapid iteration.
-->

Emberは機能が豊富でいろいろな操作ができるウェブサイトを作るために設計されたJavaScriptのフロントエンドフレームワークです。
そのため、モダンなWebアプリケーション内の複雑なものをうまく扱うために必要な多くの機能はもちろん、迅速なイテレーションを可能にする統合された開発ツールキットの両方を開発者に提供します。

<!--
Some of these features that you'll learn about in the guides are:
-->

それらの機能のうち、あなたがこのガイドで学ぶのは、

<!--
* [Ember CLI](./configuring-ember/configuring-ember-cli/) - A robust development toolkit to create, develop, and build Ember applications. When you see an `$ ember <command>` instruction throughout the guides, that's Ember CLI!
* [Routing](./routing) - The central part of an Ember application. Enables developers to drive the application state from the URL.
* [Templating engine](./templates/handlebars-basics/) - Use Handlebars syntax to write your application's templates
* [Data layer](./models/) - Ember Data provides a consistent way to communicate with external APIs and manage application state
* [Ember Inspector](./ember-inspector/) - A browser extension, or bookmarklet, to inspect your application live. It's also useful for spotting Ember applications in the wild, try to install it and open up the [NASA website](https://www.nasa.gov/)!
-->

* [Ember CLI](./configuring-ember/configuring-ember-cli/) - Emberアプリケーションの作成、開発、ビルドのための強力な開発ツールキット。このガイドを通して目にする`$ ember <command>` という指示はEmber CLIを使っています。
* [ルーティング](./routing) - Emberアプリケーションの中心部。開発者がURLからアプリケーションの状態を扱えるようにします。
* [データレイヤー](./models) - Ember DATAは外部のAPIと通信しデータを永続化しアプリケーションの状態を管理する機能を提供します。
* [Emberインスペクター](./ember-inspector) - 動作中のアプリケーションを調査するためのブラウザエクステンション、またはブックマークレット。野に放たれたEmberアプリケーションを見つけるのにも使えます。インストールして[NASAのウェブサイト](https://www.nasa.gov/)を見てみましょう！

<!--
## Organization
-->

<h2 id="toc_what-is-ember" class="anchorable-toc">ガイドの構成</h2>

<!--
On the left side of each Guides page is a table of contents,
organized into sections that can be expanded to show the topics
they cover. Both the sections and the topics within each section are
ordered from basic to advanced concepts.
-->

各ページの左側にあるものは、区分と項目から成る目次です。部を開くとその中にある項目が展開され、項目をクリックしてページを開くことができます。
区分と項目は、基礎的なものから高度なものの順に並んでいます。

<!--
The Guides are intended to contain practical explanations of how to
build Ember apps, focusing on the most widely-used features of Ember.js.
For comprehensive documentation of every Ember feature and API, see the
[Ember.js API documentation](http://emberjs.com/api/).
-->

本ガイドは、Emberアプリを作り方の実用的な説明が含まれるよう意図されています。
Ember.jsのよく使われる機能を焦点にしています。
各Emberの機能とAPIについては、包括的な[Ember.js API ドキュメント](http://emberjs.com/api/)(英語)を参照してください。

<!--
The Guides begin with an explanation of how to get started with Ember,
followed by a tutorial on how to build your first Ember app.
If you're brand new to Ember,
we recommend you start off by following along with these first two sections of the Guides.
-->

本ガイドは、Emberの始め方から始まり、その次にチュートリアルで実際にアプリを作ります。
Emberは初めてという方は、[はじめに](./getting-started/quick-start)と[チュートリアル](./tutorial/ember-cli)から始めることをお勧めします。

<!--
## Assumptions
-->

<h2 id="toc_assumptions" class="anchorable-toc">対象の読者レベル</h2>

<!--
While we try to make the Guides as beginner-friendly as we can,
we must establish a baseline so that the guides can keep focused on Ember.js functionality.
We will try to link to appropriate documentation whenever a concept is introduced.
-->

本ガイドはなるべく初心者向けに作っていますが、内容をEmber.jsの機能に絞るために、基準を設けなければなりません。

あるコンセプトや概念が登場した時はなるべく適切なドキュメントにリンクするようにしています。

<!--
To make the most out of the guides, you should have a working knowledge of:
-->

本ガイドを最大限活用するには、以下の実用的な知識が必要です。

<!--
* **HTML, CSS, JavaScript** - the building blocks of web pages. You can find documentation of each of these technologies at the [Mozilla Developer Network][mdn].
* **Promises** - the native way to deal with asynchrony in your JavaScript code. See the relevant [Mozilla Developer Network][promises] section.
* **ES2015 modules** - you will better understand [Ember CLI's][ember-cli] project structure and import paths if you are comfortable with [JavaScript Modules][js-modules].
* **ES2015 syntax** - Ember CLI comes with Babel.js by default so you can
take advantage of newer language features such as arrow functions, template
strings, destructuring, and more. You can check the
[Babel.js documentation][babeljs] or read [Understanding ECMAScript 6][es6]
online.
-->


* **HTML、CSS、JavaScript** - Webページを作のに必要なもの。[MDN ウェブドキュメント](https://developer.mozilla.org/ja/)にドキュメントがあります。
* **Promises** - JavaScriptで非同期処理を行う方法。 [MDN ウェブドキュメントのPromise](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Promise)を参照。
* **ES2015 modules**  [JavaScript Modules][js-modules]に慣れていれば、Ember CLIのプロジェクトのディレクトリ構成とインポートのパスを理解しやすいでしょう。
* **ES2015の構文** Ember CLIはBabel.jsがデフォルトで入っており、アロー関数やテンプレート文字列などの新しい言語機能を活用できます。[Babel.js ドキュメント][babeljs](英語)や[Understanding ECMAScript 6][es6](英語)をオンラインで確認できます。


<!--
## A Note on Mobile Performance
-->

<h2 id="toc_a-note-on-mobile-performance" class="anchorable-toc">モバイルのパフォーマンスについて</h2>

<!--
Ember will do a lot to help you write fast apps, but it can't prevent you from
writing a slow one. This is especially true on mobile devices. To deliver a great
experience, it's important to measure performance early and often, and with a diverse
set of devices.
-->

Emberは、あなたが速く動作するアプリを書けるようにたくさんの手助けをしてくれますが、あなたが遅いアプリを書くことは防げません。特にモバイル端末においては。
ユーザ良い体験を提供するには、計測に使うのに相応しいデバイスで早い段階からよくパフォーマンスの計測することが重要です。

<!--
Make sure you are testing performance on real devices. Simulated mobile
environments on a desktop computer give an optimistic-at-best representation of
what your real world performance will be like. The more operating systems and
hardware configurations you test, the more confident you can be.
-->

パフォーマンスのテストは本物のデバイス上で行いましょう。
デスクトップコンピュータでシミュレートされたモバイル環境では、実世界のパフォーマンスがどのようになるかをか最高に楽観的に表現します。

<!--
Due to their limited network connectivity and CPU power, great performance on
mobile devices rarely comes for free. You should integrate performance testing
into your development workflow from the beginning. This will help you avoid
making costly architectural mistakes that are much harder to fix if you only
notice them once your app is nearly complete.
-->

限られたネットワーク接続やCPUパワーにより、モバイル端末で良いパフォーマンスを出すのは容易ではありません。
開発フローに最初からパフォーマンステストを取り入れるべきです。
そうすることで、アプリの完成間近になって気がつくと修正するのに高く費用のかかるアーキテチャーの間違いを避けれるでしょう。

<!--
In short:
-->

まとめると、

<!--
1. Always test on real, representative mobile devices.
2. Measure performance from the beginning, and keep testing as your app
   develops.
-->

1. 常に代表的な本物のモバイル端末でテストする
2. 最初からパフォーマンス計測し、開発と共にテストも継続する

<!--
These tips will help you identify problems early so they can be addressed systematically, rather than
in a last-minute scramble.
-->

これらの秘訣は、リリース直前に混乱するのではなく、問題を早く発見しきちんと取り組む助けになります。

<!--
## Reporting a problem
-->

<h2 id="toc_reporting-a-problem" class="anchorable-toc">問題の報告</h2>

(訳注: この内容は英語のドキュメント内容を訳したものです。日本語訳のドキュメントの問題報告方法は現在準備中です。)

<!--
Typos, missing words, and code samples with errors are all considered
documentation bugs. If you spot one of them, or want to otherwise improve
the existing guides, we are happy to help you help us!
-->

誤字脱字やサンプルコードにエラーがあるものはドキュメントのバグだと捉えています。
もし見つけたり、既存のガイドを改善したい場合は是非ご協力ください。

<!--
Some of the more common ways to report a problem with the guides are:
-->

ガイドに関する問題の報告方法は以下になります。

<!--
* Using the pencil icon on the top-right of each guide page
* Opening an issue or pull request to [the GitHub repository][gh-guides]
-->

* 各ページの右上にある鉛筆マーク使う
* GitHubのレポジトリ上でissueかプルリクエストを作成する

<!--
Clicking the pencil icon will bring you to GitHub's editor for that
guide so you can edit right away, using the Markdown markup language.
This is the fastest way to correct a typo, a missing word, or an error in
a code sample.
-->

鉛筆マークをクリックすると、GitHubに飛びそのページのソースであるマークダウンファイルの編集画面が開きますので、そこですぐに変更することができます。
サンプルコードのエラーや誤字脱字を修正するに一番速いやり方です。

<!--
If you wish to make a more significant contribution be sure to check our
[issue tracker][gh-guides-issues] to see if your issue is already being
addressed. If you don't find an active issue, open a new one.
-->

もしもっと大きく貢献されたければ、[GitHubでissue][gh-guides-issues]を確認し、同じものがないか確認してください。アクティブなものがない場合は、新しいissueを作成してください。

<!--
If you have any questions about styling or the contributing process, you
can check out our [contributing guide][gh-guides-contributing]. If your
question persists, reach us at `#-team-learning` on the [Slack
group][slackin].
-->

貢献のプロセスやスタイルについて何か質問がありましたら、[コントリビューションガイド][gh-guides-contributing](英語)を確認してください。確認しても質問がある場合は、[Slackグループ][slackin]の`#-team-learning`をで呼びかけてください。

<!--
Good luck!
-->

グッドラック👍


[ember-cli]: https://ember-cli.com/

[mdn]: https://developer.mozilla.org/en-US/docs/Web
[promises]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[js-modules]: http://jsmodules.io/
[babeljs]: https://babeljs.io/docs/learn-es2015/
[es6]: https://leanpub.com/understandinges6/read

[gh-guides]: https://github.com/emberjs/guides/
[gh-guides-issues]: https://github.com/emberjs/guides/issues
[gh-guides-contributing]: https://github.com/emberjs/guides/blob/master/CONTRIBUTING.md

[slackin]: https://ember-community-slackin.herokuapp.com/
