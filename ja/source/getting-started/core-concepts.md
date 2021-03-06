<!--
Before you start writing any Ember code, it's a good idea to get an overview of how an
Ember application works.
-->

Emberでコードを書き始める前に、Emberアプリケーションがどのように動くのか概要を知っておくと良いでしょう。

![Emberのコアコンセプト](../../images/ember-core-concepts/ember-core-concepts.png)

<!--
## Router and Route Handlers
-->

## ルーターとルートハンドラ

<!--
Imagine we are writing a web app for a site that lets users list their properties to rent. At any given time, we should be able to answer questions about the current state like _What rental are they looking at?_ and _Are they editing it?_ In Ember, the answer to these questions is determined by the URL.
The URL can be set in a few ways:
-->

不動産賃貸サイトのWebアプリを書いていくことを想像してみましょう。
ユーザーがどの物件を見ているのか、または、その物件を編集しているのか、といった問いに対して、いつでも答えられなければなりません。
Emberでは、それらの問いにURLで特定して答えることができます。
URLが入力される方法はいくつか存在します。

<!--
* The user loads the app for the first time.
* The user changes the URL manually, such as by clicking the back button or by editing the address bar.
* The user clicks a link within the app.
* Some other event in the app causes the URL to change.
-->

* ユーザーがアプリにアクセスする
* ユーザーが手動で変える、ブラウザバックしたりアドレスバーで変更するなど
* アプリ内のリンクをクリックする
* アプリ内のその他のイベントによりURLが変更される

<!--
No matter how the URL gets set, the first thing that happens is that the Ember router maps the URL to a route handler.
-->

URLがどう変わっても、Emberではまず最初にルータがそのURLをルートハンドラにマップします。

<!--
The route handler then typically does two things:
-->

そしてルートハンドラは主に2つのことをします。

<!--
* It renders a template.
* It loads a model that is then available to the template.
-->

* テンプレートの描画
* モデルのロード(テンプレート内ではロード後に使える)

<!--
## Templates
-->

## テンプレート

<!--
Ember uses templates to organize the layout of HTML in an application.
-->

Emberアプリケーション内のHTMLはテンプレートから編成されます。

<!--
Most templates in an Ember codebase are instantly familiar, and look like any
fragment of HTML. For example:
-->

Emberアプリケーションのテンプレートのほとんどは見た感じHTMLと同じように見えるでしょう。例えば、


<!--
```handlebars
<div>Hi, this is a valid Ember template!</div>
```
-->

```handlebars
<div>やぁ！これはEmberの正しいテンプレートです！</div>
```


<!--
Ember templates use the syntax of [Handlebars](http://handlebarsjs.com)
templates. Anything that is valid Handlebars syntax is valid Ember syntax.
-->

Emberのテンプレートは[Handlebars(ハンドルバーズ)](http://handlebarsjs.com)の構文を使います。
Handlebarsの構文で正しいものはどれもEmberの構文でも正しいです。

<!--
Templates can also display properties provided to them from their context, which is either a component or a route's controller. For example:
-->

テンプレートはプロパティを表示することができます。プロパティはテンプレートのコンテキストであるコンポーネントかルートのコントローラから与えられます。例えば、

<!--
```handlebars
<div>Hi {{name}}, this is a valid Ember template!</div>
```
-->

```handlebars
<div>やぁ {{name}}, これはEmberの正しいテンプレートです!</div>
```


<!--
Here, `{{name}}` is a property provided by the template's context.
-->

この`{{name}}`はテンプレートのコンテキストから与えられるプロパティです。

<!--
Besides properties, double curly braces (`{{}}`) may also contain
helpers and components, which we'll discuss later.
-->

プロパティに加え、二重波括弧`{{}}`は、ヘルパーやコンポーネントにも使われます。
コンポーネントについては後述します。

<!--
## Models
-->

## モデル

<!--
Models represent persistent state.
-->

モデルは永続化された状態を表します。

<!--
For example, a property rentals application would want to save the details of a rental when a user publishes it, and so a rental would have a model defining its details, perhaps called the _rental_ model.
-->

例えば、賃貸物件アプリで、ユーザーが物件を公開する時に物件の詳細情報(details)を保存したい場合、アプリ内にdetailsを定義したrentalモデルを持つことになるでしょう。

<!--
A model typically persists information to a web server, although models can be configured to save to anywhere else, such as the browser's Local Storage.
-->

モデルでは情報の永続化にWebサーバが主に使われますが、設定次第でブラウザのローカルストレージなども可能です。

<!--
## Components
-->

## コンポーネント

<!--
While templates describe how a user interface looks, components control how the user interface _behaves_.
-->

テンプレートにはUIの見た目を記述し、コンポーネントにはUIの振る舞いを記述します。

<!--
Components consist of two parts: a template written in Handlebars, and a source file written in JavaScript that defines the component's behavior. For example, our property rental application might have a component for displaying all the rentals called `all-rentals`, and another component for displaying an individual rental called `rental-tile`. The `rental-tile` component might define a behavior that lets the user hide and show the image property of the rental.
-->

コンポーネントは、Handlebars構文で書かれたテンプレートと、振る舞いを書いたJavaScriptの2つから成り立ちます。例えば、賃貸物件アプリで、全物件を表示する`all-rentals`コンポーネントがあり、一つの物件を表示する`rental-tile`コンポーネントがあるとして、`rental-tile`コンポーネントに、ユーザーが物件画像の表示や非表示ができるようにコードを書く、という感じです。

<!--
Let's see these core concepts in action by building a property rental application in the next lesson.
-->

では、これらのコアコンセプトを次のレッスンで賃貸不動産アプリケーションを実際に作りながら見ていきましょう。
