<!--
You'll notice standard JavaScript class patterns and the new ES2015
classes aren't widely used in Ember. Plain objects can still be found,
and sometimes they're referred to as "hashes".
-->

標準のJavaScriptクラスパターンや、ES2015クラスはEmberでは広く使用されていないことに気がつくでしょう。
プレーンオブジェクトは使われているところがあり、ハッシュととして参照されているところもあります。

<!--
JavaScript objects don't support the observation of property value changes.
Consequently, if an object is going to participate in Ember's binding
system you may see an `Ember.Object` instead of a plain object.
-->

JavaScriptオブジェクトは、プロパティ値の変更の監視をサポートしていません。
したがって、Emberのバインディングシステムに入れるオブジェクトは、プレーンオブジェクトの代わりに`Ember.Object`が使われます。

<!--
[Ember.Object](https://www.emberjs.com/api/ember/2.16/modules/@ember%2Fobject) also provides a class system, supporting features like mixins
and constructor methods. Some features in Ember's object model are not present in
JavaScript classes or common patterns, but all are aligned as much as possible
with the language and proposed additions.
-->

[Ember.Object](https://www.emberjs.com/api/ember/2.16/modules/@ember%2Fobject)は、mixinやコンストラクタメソッドなどの機能をサポートするクラスシステムも提供します。
Emberのオブジェクトモデルの一部の機能は、JavaScriptクラスや一般的なパターンには存在しませんが、言語と提案されている追加機能にできる限り合わせています。

<!--
Ember also extends the JavaScript `Array` prototype with its
[Ember.Enumerable](http://emberjs.com/api/classes/Ember.Enumerable.html) interface to provide change observation for arrays.
-->

また、Emberは配列の変更を監視できるように、[Ember.Enumerable](http://emberjs.com/api/classes/Ember.Enumerable.html)インターフェイスを使用してJavaScript`Array`プロトタイプを拡張します。

<!--
Finally, Ember extends the `String` prototype with a few [formatting and
localization methods](https://www.emberjs.com/api/ember/2.16/classes/String).
-->

最後に、Emberは[フォーマットとローカライゼーション](https://www.emberjs.com/api/ember/2.16/classes/String)のために`String`プロトタイプを拡張します。
