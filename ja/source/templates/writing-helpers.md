<!--
Helpers allow you to add additional functionality to your
templates beyond what is included out-of-the-box in Ember. Helpers are
most useful for transforming raw values from models and components into
a format more appropriate for your users.
-->

ヘルパーを使用すると、Emberですぐに使用できる機能に止まらず、さらに機能をテンプレートに追加することができます。
ヘルパーは、モデルやコンポーネントの生の値をユーザーにとってより適切な形式に変換するのに最も役立ちます。

<!--
For example, imagine we have an `Invoice` model that contains a
`totalDue` attribute, which represents the total amount due for that
invoice.  Because we do not want our company to go out of business due
to strange JavaScript rounding errors, [we store this value in cents
instead of a floating point dollar value][currency-stackoverflow].
-->

例えば、請求書(`Invoice`)の総額を表す`totalDue`属性を含む請求書モデルがあるとします。
JavaScriptの奇妙な丸めの誤差によって会社を廃業させたくないので、[この値を浮動小数点のドル値の代わりにセントで保存][currency-stackoverflow]することにします。

[currency-stackoverflow]: http://stackoverflow.com/a/3730040

<!--
However, if we display dollar values to our users as "100¢" instead of
"$1.00", they may be very confused. We can write a helper to
format these values into the appropriate human-readable form.
-->

ですが、ドル値を「$1.00」ではなく「100¢」としてユーザーに表示すると、非常に混乱することがあります。
そこで、これらの値を人間が読める適切な形式に変換するヘルパーを書きます。

<!--
Let's create a `format-currency` helper that takes an integer count of
cents and turns it into formatted dollars.
-->

整数のセントを受け取り、ドル形式に変換する`format-currency`ヘルパーを作成しましょう。

<!--
To use the `format-currency` helper, you call it using curly braces in
your template:
-->

`format-currency`ヘルパーを使うには、テンプレートで中括弧を使用して呼び出します。

```handlebars
Your total is {{format-currency model.totalDue}}.
```

<!--
Let's now implement the helper. Helpers are functions that take
one or more inputs and return a single output that should be put into
the HTML.
-->

ヘルパーを実装しましょう。
ヘルパーは、1つ以上の入力を受け取り、それをHTMLに挿入する1つの出力にして返す関数です。

<!--
To add a new helper, create a file with the name of the helper you want
(e.g. `format-currency.js`) in your application's `helpers` directory.
You can also have Ember generate the file for you from the command line:
-->

新しいヘルパーを追加するには、アプリケーションの`helpers`ディレクトリにヘルパーの名前(例: `format-currency.js`など)を含むファイルを作成します。
Ember CLIでファイルを生成することもできます。

```shell
ember generate helper format-currency
```

<!--
That file should export a function wrapped with [`Ember.Helper.helper()`](http://emberjs.com/api/classes/Ember.Helper.html#method_helper):
-->

ヘルパーのファイルでは、[`Ember.Helper.helper()`](http://emberjs.com/api/classes/Ember.Helper.html#method_helper)でラップされた関数をエクスポートします。

```app/helpers/format-currency.js
import { helper } from '@ember/component/helper';

export function formatCurrency([value, ...rest]) {
  let dollars = Math.floor(value / 100);
  let cents = value % 100;
  let sign = '$';

  if (cents.toString().length === 1) { cents = '0' + cents; }
  return `${sign}${dollars}.${cents}`;
}

export default helper(formatCurrency);
```

<!--
In this example, the function receives a dollar amount in cents as the first
parameter (`value`). We then use regular JavaScript to turn the
count of cents into a formatted string, like `"$5.00"`.
-->

この例では、関数は第1引数(`value値`)としてセント単位の金額を受け取ります。
次に、通常のJavaScriptを使用してセントから`"$5.00"`のように文字列にフォーマットします。

<!--
Whenever you use your helper in a template, Ember will call this
function and insert whatever you return from the helper into the DOM.
-->

このヘルパーをテンプレートで使用する時はいつも、Emberはこの関数を呼び出して、ヘルパーが返したものなら何
であれDOMに挿入します。

<!--
So, if we want to display a purchase total we can pass the value into the template in cents:
-->

したがって、購入合計額を表示したい場合は、値をテンプレートにセントで渡すことができます。

```hbs
Your total is {{format-currency 250}}.
```

<!--
And Ember makes use of our new helper function to replace the content inside the ```{{ }}``` with the formatted amount.
-->

そして、Emberはそのヘルパー関数を使って、`{{ }}`の中のコンテンツをフォーマットされた金額に置き換えます。

```hbs
Your total is $2.50.
```

<!--
Whenever the arguments you've passed to a helper change, whether they
come from a model or a component, Ember will automatically call your
helper again with the new values and keep the page up-to-date.
-->

モデルやコンポーネントのどちらから渡されても、渡されたものが更新されれば、Emberは自動的にヘルパーを新しい値で呼び出し、ページを最新の状態に保ちます。

<!--
### Helper Names
-->

### ヘルパーの名前

<!--
Unlike [components](../../components/defining-a-component/), which require a dash in the name to follow the Custom Element spec, helper names can be single or multi-word. If your helper's name is multi-word, it should be dasherized as the examples on this page.
-->

カスタム要素の仕様に従うために名前にダッシュを要求する[コンポーネント](../../components/defining-a-component/)とは異なり、ヘルパー名は単一または複数の単語にすることができます。
ヘルパーの名前が複数の単語である場合は、このページの例にあるように、ダッシュで単語間を繋ぎます。

<!--
### Helper Arguments
-->

### ヘルパーの引数

<!--
You can pass one or more arguments to be used
inside the function. In the above example, we passed the amount in cents
as the first and only argument.
-->

関数内で使用される1つまたは複数の引数を渡すことができます。
上の例では、引数は1つだけで、セントで金額を渡しました。

<!--
To pass multiple arguments to a helper, add them as a space-separated
list after the helper name:
-->

複数の引数をヘルパーに渡すには、ヘルパー名の後に空白で区切って追加します。

```handlebars
{{my-helper "hello" "world"}}
```

<!--
An array of these arguments is passed to the helper function:
-->

引数が複数の時は、配列としてヘルパー関数に渡されます。

```app/helpers/my-helper.js
import { helper } from '@ember/component/helper';

export function myHelper(params) {
  let [arg1, arg2] = params;

  console.log(arg1); // => "hello"
  console.log(arg2); // => "world"
});

export default helper(myHelper);
```

<!--
You can use JavaScript's destructuring assignment shorthand to clean up
the code. This example is equivalent to the above example (note the function signature):
-->

JavaScriptの分割代入を使用して、コードをクリーンアップすることができます。
以下の例は上記の例に相当します(関数の書き方を見比べてください)。

```app/helpers/my-helper.js
import { helper } from '@ember/component/helper';

export function myHelper([arg1, arg2]) {
  console.log(arg1); // => "hello"
  console.log(arg2); // => "world"
});

export default helper(myHelper);
```

<!--
### Named Arguments
-->

### 名前付き引数

<!--
Normal arguments are useful for passing data to be transformed into
helper functions. However, because the order in which you pass arguments
matters, it is usually best not to have helpers take more than one or
two of them.
-->

通常の引数は、ヘルパー関数に変換するデータを渡すのには便利です。
しかし、引数を渡す順序が重要なので、通常はヘルパーが2つ以上の引数を取らないようにするのがベストです。

<!--
That said, sometimes you may want to make behavior of helpers
configurable by the developers that call them from their templates. For
example, let's abandon our Americentric ways and update our
`format-currency` helper to take an optional configuration for which
currency symbol to display.
-->

ヘルパーをテンプレートから呼び出す時に、開発者がヘルパーの動作を設定できるようにしたい場合があります。
例えば、アメリカ風なやり方をやめて、`format-currency`ヘルパーを変更して、表示する通貨記号をオプションで設定できるようにするとしましょう。

<!--
Helpers allow you to pass named arguments as a JavaScript
object that contains the name of the argument along with an associated
value.  The order in which named arguments are supplied does not affect
functionality.
-->

ヘルパーを使用すると、指定された引数を、関連付けられた値とともに引数の名前を含むJavaScriptオブジェクトとして渡すことができます。
名前付き引数の指定順序は機能に影響しません。

<!--
In this example, we can pass a `sign` argument to our `format-currency`
helper:
-->

この例では、`format-currency`ヘルパーに`sign`引数を渡すしています。

```handlebars
{{format-currency 350 sign="£"}}
```

<!--
We'd like our helper to print pounds sterling rather than US dollars:
-->

ヘルパーに米ドルではなく英ポンドを表示させます。

```handlebars
£3.50
```

<!--
The object containing named arguments is passed as the second argument
to the helper function.  Here is our example from above, updated to
support the optional `sign` option:
-->

名前付き引数を含むオブジェクトは、第2引数としてヘルパー関数に渡されます。
以下の例で上記の例に`sign`オプションを追加しています。

```app/helpers/format-currency.js
export default helper(myHelper)

export function formatCurrency([value, ...rest], namedArgs) {
  let dollars = Math.floor(value / 100);
  let cents = value % 100;
  let sign = namedArgs.sign === undefined ? '$' : namedArgs.sign;

  if (cents.toString().length === 1) { cents = '0' + cents; }
  return `${sign}${dollars}.${cents}`;
});

export default helper(formatCurrency);
```

<!--
You can pass as many named arguments as you'd like. They get added to the
`namedArgs` argument passed to the function:
-->

名前付き引数は、必要な分だけ渡すことができます。
関数渡された名前付き引数は`namedArgs`に追加されます。

```handlebars
{{my-helper option1="hello" option2="world" option3="goodbye cruel world"}}
```

```app/helpers/my-helper.js
export default helper(myHelper)

export function myHelper(params, namedArgs) {
  console.log(namedArgs.option1); // => "hello"
  console.log(namedArgs.option2); // => "world"
  console.log(namedArgs.option3); // => "goodbye cruel world"
});

export default helper(myHelper)
```

<!--
You can use JavaScript's destructuring assignment shorthand in this case
as well to clean up the above code:
-->

この場合も同様に、JavaScriptの分割代入を使って上記のコードをクリーンアップすることができます。

```app/helpers/my-helper.js
export default helper(myHelper)

export function myHelper(params, { option1, option2, option3 }) {
  console.log(option1); // => "hello"
  console.log(option2); // => "world"
  console.log(option3); // => "goodbye cruel world"
});

export default helper(myHelper);
```

<!--
In sum, arguments are good for passing values:
-->

要約すると、通常の引数は値を渡すのに適しています。

```handlebars
{{format-date currentDate}}
```

<!--
Hashes are useful for configuring the behavior of a helper:
-->

ハッシュはヘルパーの動作を設定するのに便利です。

```handlebars
{{print-current-date format="YYYY MM DD"}}
```

<!--
You can have as many of both as you want, so long as the parameters come
first:
-->

通常の引数は先頭に置き、名前付き引数はその後ろに置くことで、両方とも必要な数だけ渡すことができます。

```handlebars
{{format-date-and-time date time format="YYYY MM DD h:mm" locale="en"}}
```

<!--
The above example contains two arguments:
-->

上記の例は以下の2つの引数を持っています。

* `date`
* `time`

<!--
And two named arguments:
-->

そして以下の2つの名前付き引数を持っています。

* `format="YYY MM DD h:mm"`
* `locale="en"`

<!--
### Class-based Helpers
-->

### クラスベースのヘルパー

<!--
By default, helpers are _stateless_. They are passed inputs (parameters
and a hash), they perform an operation on those inputs, and return a
single output. They have no side-effects and don't save any information
that is used on subsequent runs of the function.
-->

デフォルトでは、ヘルパーはステートレスです。
ヘルパーは入力(パラメータとハッシュ)を渡され、入力を処理し、単一の出力を返します。
ヘルパーには副作用がなく、後続の処理で使われる情報は保存しません。

<!--
In some situations, however, you may need to write a helper that interacts with
the rest of your application. You can create class-based helpers that have
access to services in your application, and can optionally save state as well,
although this is usually unnecessary and error-prone.
-->

ただし、状況によっては、アプリケーションの他の部分とやりとりするヘルパーを作成する必要があります。
その場合、アプリケーション内のサービスにアクセスできるクラスベースのヘルパーを作成することもできます。
状態を保存することもできますが、通常はその必要はなく、また、エラーが発生しやすくなります。

<!--
To create a class-based helper, rather than exporting a simple function, you
should export a subclass of [`Ember.Helper`][1]. Helper classes must contain a
[`compute`][2] method that behaves the same as the function passed to
[`Ember.Helper.helper`][3].  In order to access a service, you must first inject it
into the class-based helper.  Once added, you can call the service's methods or
access its properties from within the `compute()` method.
-->

単純な関数をエクスポートするのではなく、クラスベースのヘルパーを作成するには、[`Ember.Helper`][1]のサブクラスをエクスポートする必要があります。
ヘルパークラスには、[`Ember.Helper.helper`][3]に渡される関数と同じように動作する[`compute`][2]メソッドが含まれている必要があります。
サービスにアクセスするには、まずクラスベースのヘルパーにサービスを注入(inject)する必要があります。
追加すると、サービスのメソッドを呼び出すことも、`compute()`メソッドの中からプロパティにアクセスすることもできます。

<!--
As an exercise, here is the above `format-currency` helper re-factored
into a class-based helper:
-->

練習として、上記の`format-currency`ヘルパーをクラスベースのヘルパーにリファクタリングしました。

```app/helpers/format-currency.js
import Helper from '@ember/component/helper';

export default Helper.extend({
  compute([value, ...rest], hash) {
    let dollars = Math.floor(value / 100);
    let cents = value % 100;
    let sign = hash.sign === undefined ? '$' : hash.sign;

    if (cents.toString().length === 1) { cents = '0' + cents; }
    return `${sign}${dollars}.${cents}`;
  }
});
```

<!--
This is exactly equivalent to the `format-currency` example above. You
can think of the function version as a shorthand for the longer class
form if it does not require dependency injection.
-->

上記の`format-currency`の例とまったく同じ動作をします。
依存注入(Dependency Injection)を必要としない場合は、関数をエクスポートしている方をクラスベースの省略版と捉えることもできます。

<!--
As another example, let's make a helper utilizing an authentication
service that welcomes users by their name if they're logged in:
-->

別の例として、ユーザーがログインしている時に、ユーザー名を表示して歓迎する認証サービスを利用するヘルパーを作ってみましょう。

```app/helpers/is-authenticated.js
import Helper from '@ember/component/helper';
import { inject as service } from '@ember/service;

export default Helper.extend({
  authentication: service(),

  compute() {
    let authentication = this.get('authentication');

    if (authentication.get('isAuthenticated')) {
      return 'Welcome back, ' + authentication.get('username');
    } else {
      return 'Not logged in';
    }
  }
});
```

<!--
### Escaping HTML Content
-->

### HTMLコンテンツのエスケープ

<!--
To protect your application from cross-site scripting attacks (XSS),
Ember automatically escapes any value you return from a helper so that
the browser will not interpret it as HTML.
-->

Emberは、クロスサイトスクリプティング攻撃(XSS)からアプリケーションを保護するため、ブラウザがHTMLとして解釈しないよう、ヘルパーの戻り値を自動的にエスケープします。

<!--
For example, here's a `make-bold` helper that returns a string containing HTML:
-->

例えば、HTMLを含む文字列を返す`make-bold`ヘルパーがあるとします。

```app/helpers/make-bold.js
import { helper } from '@ember/component/helper';

export function makeBold([param, ...rest]) {
  return `<b>${param}</b>`;
});

export default helper(makeBold);
```

<!--
You can invoke it like this:
-->

以下のようにして呼び出すと、

```handlebars
{{make-bold "Hello world"}}
```

<!--
Ember will escape the HTML tags, like this:
-->

Emberは以下のようにHTMLタグをエスケープします。

```handlebars
&lt;b&gt;Hello world&lt;/b&gt;
```

<!--
This shows the literal string `<b>Hello world</b>` to the user, rather
than the text in bold as you probably intended. We can tell Ember not to
escape the return value (that is, that it is _safe_) by using the
[`htmlSafe`][4] string utility:
-->

その結果、テキストは太文字で表示されず、代わりに`<b>Hello world</b>`というテキストが表示されます。
[`htmlSafe`][4]を使用して、戻り値をエスケープしないようにEmberに指示できます。

```app/helpers/make-bold.js
import { helper } from '@ember/component/helper';
import { htmlSafe } from '@ember/string';

export function makeBold([param, ...rest]) {
  return htmlSafe(`<b>${param}</b>`);
});

export default helper(makeBold);
```

<!--
If you return a `SafeString` (a string that has been wrapped in a call
to [`htmlSafe`][4]), Ember knows that you have vouched on its behalf that it
contains no malicious HTML.
-->

`SafeString`([`htmlSafe`][4]の呼び出しでラップされた文字列)を返すと、Emberは悪意のあるHTMLが含まれていないことが保証されていることを認識します。

<!--
However, note that in the above code we may have inadvertently
introduced an XSS vulnerability into our application! By blindly marking
the string as safe, a malicious user could get their own HTML into our
app, allowing them to do things like access sensitive customer data.
-->

ただし、上記のコードでは、アプリケーションにXSSの脆弱性が誤って投入されている可能性があります。
盲目的に文字列の安全性を保証すると、悪意のあるユーザーが独自のHTMLをアプリケーションに入れ込むことで、機密性の高い顧客データなどにアクセスできるようになってしまいます。

<!--
For example, imagine that we have a chat app  and use our `make-bold`
helper to welcome the new users into the channel:
-->

例えば、チャットアプリがあり、新しいユーザーをチャンネルに歓迎するために`make-bold`ヘルパーを使用しているとします。

```handlebars
Welcome back! {{make-bold model.firstName}} has joined the channel.
```
<!--
Now a malicious user simply needs to set their `firstName` to a string
containing HTML (like a `<script>` tag that sends private customer data
to their server, for example) and every user in that chat room has been
compromised.
-->

悪意のあるユーザーは`firstName`をHTMLを含む文字列(たとえば、プライベート顧客データをサーバーに送信する`<script>`タグなど)に設定すると、そのチャットルーム内のすべてのユーザーに危害が及んでしまいます。

<!--
In general, you should prefer using components if you are wrapping
content in HTML. However, if you really want to include a mix of HTML
and values from models in what you return from the helper, make sure you
escape anything that may have come from an untrusted user with the
`escapeExpression` utility:
-->

一般に、HTMLでコンテンツをラップする場合は、コンポーネントを使用する方がよいでしょう。
しかし、ヘルパーの戻り値にHTMLとモデルの値を混在させたい場合は、信頼できないユーザーから来た可能性のあるものを`escapeExpression`ユーティリティで確実にエスケープしてください。

```app/helpers/make-bold.js
import Ember from "ember";
import { helper } from '@ember/component/helper';
import { htmlSafe } from "@ember/string";

export function makeBold([param, ...rest]) {
  let value = Ember.Handlebars.Utils.escapeExpression(param);
  return htmlSafe(`<b>${value}</b>`);
});

export default helper(makeBold);
```
<!--
Now the value passed into the helper has its HTML escaped, but the trusted
`<b>` tags that we want to wrap the value in are _not_ escaped. A
malicious user setting their `firstName` to something containing HTML
would see this:
-->

上記の例ではヘルパーに渡された値がHTMLがエスケープされています。
値をラップしている`<b>`タグは信頼できるためエスケープされていません。
これにより、`firstName`にHTMLを含めた値に設定した悪意のあるユーザーには、以下のように表示されるようになります。

```handlebars
Welcome back! <b>&lt;script
type="javascript"&gt;alert('pwned!');&lt;/script&gt;</b> has joined the channel.
```
[1]: https://www.emberjs.com/api/ember/2.16/classes/Helper
[2]: https://www.emberjs.com/api/ember/2.16/classes/Helper/methods/compute?anchor=compute
[3]: http://emberjs.com/api/classes/Ember.Helper.html#method_helper
[4]: https://www.emberjs.com/api/ember/2.16/classes/@ember%2Fstring/methods/htmlSafe?anchor=htmlSafe
