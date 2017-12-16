<!--
Many new features were introduced to JavaScript with the release of newer specifications like ECMAScript 2015,
also known as [ECMAScript 6 or ES6](https://developer.mozilla.org/en/docs/Web/JavaScript/New_in_JavaScript/ECMAScript_6_support_in_Mozilla).
While the Guides [assume you have a working knowledge of JavaScript](/#toc_assumptions),
not every feature of the JavaScript language may be familiar to the developer.
-->

ECMAScript2015や[ECMAScript 6 または ES6](https://developer.mozilla.org/en/docs/Web/JavaScript/New_in_JavaScript/ECMAScript_6_support_in_Mozilla)として知られる新しい仕様のリリースにより、多くの機能がJavaScriptに導入されました。
本ガイドでは[読者はJavaScriptの実用的な知識があることを想定](/#toc_assumptions)していますが、全ての機能がよく知られているとも限りません。

<!--
In this guide we will be covering some JavaScript features,
and how they are used in Ember applications.
-->

このガイドでは、Emberアプリケーション内で使われるJavaScriptの機能をいくつか抑えることができます。

<!--
## Variable declarations
-->

<h2 id="toc_variable-declarations" class="anchorable-toc">変数宣言</h2>

<!--
A variable declaration, also called binding, is when you assign a value to a variable name.
An example of declaring a variable containing the number 42 is like so:
-->

値を変数名に代入することを、変数定義または束縛と呼びます。
数値の42を持つ変数の宣言は以下のようになります。



```javascript
var myNumber = 42;
```
<!--
JavaScript initially had two ways to declare variables, globally and `var`.
With the release of ES2015, `const` and `let` were introduced.
We will go through the different ways to declare a variable,
also called bindings because they *bind* a value to a variable name,
and why modern JavaScript tends to prefer `const` and `let`.
-->

JavaScriptにはもともと変数宣言にグローバルと`var`の方法がありました。
ES2015のリリースにより、`const`と`let`が導入されました。
各変数宣言の違いと束縛、なぜモダンなJavaScriptでは`const`と`let`が好まれる傾向にあるか見ていきましょう。

<!--
### `var`
-->

<h3 id="toc_var" class="anchorable-toc"><code>var</code></h3>

<!--
Variable declarations using `var` exist in the entire body of the function where they are declared.
This is called function-scoping, the existence of the `var` is scoped to the function.
If you try to access a `var` outside of the function it is declared,
you will get an error that the variable is not defined.
-->

関数内で`var`によって宣言された変数は、その関数内で常に存在します。
これは関数スコープ(function-scoping)と呼ばれ、`var`で宣言された変数は、その関数のスコープ内で有効です。
変数が宣言されたスコープの外で、その変数にアクセスすると、変数未定義のエラーが発生します。

<!--
For our example, we will declare a `var` named `name`.
We will try to access it both inside the function and outside,
and see the results we get:
-->

例として、nameという変数を`var`を使って宣言してみましょう。
関数のスコープのうちと外で変数にアクセスすると以下のようになります。

```javascript
console.log(name); // ReferenceError: name is not defined

function myFunction() {
  var name = "Tomster";

  console.log(name); // "Tomster"
}
```

<!--
This also means that if you have an `if` or a `for` in your code and declare a `var` inside them,
you can still access the variable outside of those blocks:
-->

`if`か`for`の場合は、ブロックの外でも変数にはアクセスできます。
(スコープが分かれていないためです。)

```javascript
console.log(name); // undefined

if (true) {
  var name = "Tomster";

  console.log(name); // "Tomster"
}
```

<!--
In the previous example, we can see that the first `console.log(name)` prints out `undefined` instead of the value.
That is because of a feature of JavaScript called *hoisting*.
Any variable declaration is moved by the programming language to the top of the scope it belongs to.
As we saw at the beginning, `var` is scoped to the function,
so the previous example is the same as:
-->

上記の例では、最初の`console.log(name)` で変数の値の代わりに`undefined`が出力されました。
これはホイスティング(hoisting: 持ち上げること)というJavaScriptの仕様です。
どの変数宣言もJavaScriptによって所属するスコープの最上部に移動させられます。
ですので、上記の例では以下のように書いたのと同じことになります。

```javascript
var name;
console.log(name); // undefined

if (true) {
  name = "Tomster";

  console.log(name); // "Tomster"
}
```

<!--
### `const` and `let`
-->

<h3 id="toc_const-and-let" class="anchorable-toc"><code>const</code>と<code>let</code></h3>

<!--
There are two major differences between `var` and both `const` and `let`.
`const` and `let` are both block-level declarations, and they are *not* hoisted.
-->

`const`と`let`は`var`との大きな違いが2つあります。
`const`と`let`は両方ともブロックレベルでの宣言で、かつ、ホイスティングされません。

<!--
Because of this they are not accessible outside of the given block scope (meaning in a `function` or in `{}`) they are declared in.
You can also not access them before they are declared, or you will get a [`ReferenceError`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ReferenceError).
-->

これにより、宣言されたブロックのスコープ(`function`内や`{}`内のこと)の外では、その変数にアクセスすることができません。
また、変数宣言される前の変数へのアクセスもできず、もしアクセスすると[`ReferenceError`](https://developer.mozilla.org/ja-JP/docs/Web/JavaScript/Reference/Global_Objects/ReferenceError)エラーが発生します。


```javascript
console.log(name) // ReferenceError: name is not defined

if (person) {
  console.log(name) // ReferenceError: name is not defined
  
  let name = 'Gob Bluth'; // "Gob Bluth"
} else {
  console.log(name) // ReferenceError: name is not defined
}
```

<!--
`const` declarations have an additional restriction, they are *constant references*,
they always refer to the same thing.
To use a `const` declaration you have to specify the value it refers,
and you cannot change what the declaration refers to:
-->

`const`宣言にはさらに1つ制約があります。それは参照は常に同じで変えられないということです。
`const`を使って宣言する場合、参照する値を指定しますが、宣言後に参照するものを変えることはできません。


```javascript
const firstName; // Uncaught SyntaxError: Missing initializer in const declaration
const firstName = 'Gob';
firstName = 'George Michael'; // Uncaught SyntaxError: Identifier 'firstName' has already been declared
```

<!--
Note that `const` does not mean that the value it refers to cannot change.
If you have an array or an object, you can change their properties:
-->

`const`は変更できない値への参照ではないことに注意してください。
もし参照先が配列かオブジェクトであれば、その中身は変更することができます。

```javascript
const myArray = [];
const myObject = { name: "Tom Dale" };

myArray.push(1);
myObject.name = "Leah Silber";

console.log(myArray); // [1]
console.log(myObject); // {name: "Leah Silber"}
```

<!--
### `for` loops
-->

<h3 id="toc_for-loops" class="anchorable-toc"><code>for</code>ループ</h3>

<!--
Something that might be confusing is the behaviour of `let` in `for` loops.
-->

混乱するものの中に`for`ループ内の`let`もあるでしょう。

<!--
As we saw before, `let` declarations are scoped to the block they belong to.
In `for` loops, any variable declared in the `for` syntax belongs to the loop's block.
-->

前述にあった通り、`let`で宣言されたものは所属するブロック内で有効です。

<!--
Let's look at some code to see what this looks like.
If you use `var`, this happens:
-->

コードを見て違いを確認しましょう。
`var`を使う場合、

```javascript
for (var i = 0; i < 3; i++) {
  console.log(i) // 0, 1, 2
}

console.log(i) // 3
```

<!--
But if you use `let`, this happens instead:
-->

しかし、`let`を使う場合では、

```javascript
for (let i = 0; i < 3; i++) {
  console.log(i) // 0, 1, 2
}

console.log(i) // ReferenceError: i is not defined
```


<!--
Using `let` will avoid accidentally leaking and changing the `i` variable from outside of the `for` block.
-->

`let`を使うことで変数`i`を`for`ブロックの外にうっかり漏らしたり変更できる状態を避けることができます。

<!--
### Resources
-->

<h3 id="toc_resources" class="anchorable-toc">参考</h3>

<!--
For further reference you can consult Developer Network articles:
-->

詳しくはMDN web docsを参照してください。

* [`var`](https://developer.mozilla.org/ja-JP/docs/Web/JavaScript/Reference/Statements/var)
* [`const`](https://developer.mozilla.org/ja-JP/docs/Web/JavaScript/Reference/Statements/const)
* [`let`](https://developer.mozilla.org/ja-JP/docs/Web/JavaScript/Reference/Statements/let).
