<!--
A model is a class that defines the properties and behavior of the
data that you present to the user. Anything that the user expects to see
if they leave your app and come back later (or if they refresh the page)
should be represented by a model.
-->

モデルは、ユーザーに提示するデータのプロパティと動作を定義するクラスです。
ユーザーがアプリを離れて後で戻って来た時に(またはページのリフレッシュの場合)、ユーザーが見たいと思うものは、すべてモデルで表される必要があります。

<!--
When you want a new model for your application you need to create a new file
under the models folder and extend from `DS.Model`. This is more conveniently
done by using one of Ember CLI's generator commands. For instance, let's create
a `person` model:
-->

アプリケーションの新しいモデルを作成するには、modelsフォルダの下に新しいファイルを作成し、`DS.Model`を拡張する必要があります。
Ember CLIのジェネレータコマンドを使用すると便利です。
`person`モデルを作ってみましょう。

```bash
ember generate model person
```

<!--
This will generate the following file:
-->

これにより、次のファイルが生成されます。

```app/models/person.js
import DS from 'ember-data';

export default DS.Model.extend({
});
```

<!--
After you have defined a model class, you can start [finding](../finding-records)
and [working with records](../creating-updating-and-deleting-records) of that type.
-->

モデルクラスを定義したら、そのタイプのレコードを[検索](../finding-records)したり[操作](../creating-updating-and-deleting-records)ことができます。

<!--
## Defining Attributes
-->

## アトリビュートの定義

<!--
The `person` model we generated earlier didn't have any attributes. Let's
add first and last name, as well as the birthday, using [`DS.attr`](https://www.emberjs.com/api/ember-data/2.16/classes/DS/methods/attr?anchor=attr):
-->

以前に作成した`person`モデルにはアトリビュート(属性)がありませんでした。
[`DS.attr`](https://www.emberjs.com/api/ember-data/2.16/classes/DS/methods/attr?anchor=attr)を使って、名字と誕生日を追加しましょう。

```app/models/person.js
import DS from 'ember-data';

export default DS.Model.extend({
  firstName: DS.attr(),
  lastName: DS.attr(),
  birthday: DS.attr()
});
```

<!--
Attributes are used when turning the JSON payload returned from your
server into a record, and when serializing a record to save back to the
server after it has been modified.
-->

アトリビュートは、サーバーから返されたJSONペイロードをレコードに変換する時や、レコードを変更した後でサーバーに保存する際のレコードのシリアライズで使用されます。

<!--
You can use attributes like any other property, including as part of a
computed property. Frequently, you will want to define computed
properties that combine or transform primitive attributes.
-->

他のプロパティと同様に、算出プロパティの一部としてアトリビュートを使用できます。
多くの場合、プリミティブアトリビュートを結合または変換する算出プロパティを定義することになります。

```app/models/person.js
import DS from 'ember-data';
import { computed } from '@ember/object';

export default DS.Model.extend({
  firstName: DS.attr(),
  lastName: DS.attr(),

  fullName: computed('firstName', 'lastName', function() {
    return `${this.get('firstName')} ${this.get('lastName')}`;
  })
});
```

<!--
For more about adding computed properties to your classes, see [Computed
Properties](../../object-model/computed-properties).
-->

算出プロパティをクラスに追加する方法の詳細については、[算出プロパティ](../../object-model/computed-properties)を参照してください。

<!--
### Transforms
-->

### トランスフォーム(アトリビュートの変換)

<!--
You may find the type of an attribute returned by the server does not
match the type you would like to use in your JavaScript code. Ember
Data allows you to define simple serialization and deserialization
methods for attribute types called transforms. You can specify that
you would like a transform to run for an attribute by providing the
transform name as the first argument to the `DS.attr` method. Ember Data
supports attribute types of `string`, `number`, `boolean`, and `date`,
which coerce the value to the JavaScript type that matches its name.
-->

サーバーから返されるアトリビュートの型が、JavaScriptコードで使用する型と一致しないことがあります。
Ember Dataでは、トランスフォーム(transforms:変換、変形という意味)と呼ばれるアトリビュートタイプのシリアライズとデシリアライズの方法を簡単に定義できます。
トランスフォーム名を`DS.attr`メソッドの最初の引数として指定することで、アトリビュートの変換を実行するように指定できます。
Ember Dataは、`string`、`number`、`boolean`と`date`のアトリビュートタイプをサポートしています。
これらのタイプは、その名前と一致するJavaScriptタイプに値を変換します。

```app/models/person.js
import DS from 'ember-data';

export default DS.Model.extend({
  name: DS.attr('string'),
  age: DS.attr('number'),
  admin: DS.attr('boolean'),
  birthday: DS.attr('date')
});
```

<!--
The `date` transform will transform an
[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) string to a JavaScript
date object.
-->

`date`トランスフォームは、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)文字列をJavaScriptの日付オブジェクトに変換します。

<!--
The `boolean` transform can handle values other than `true` or
`false`. The strings `"true"` or `"t"` in any casing, `"1"`, and the number
`1` will all coerce to `true`, and `false` otherwise.
-->

`boolean`トランスフォームでは、`true`または`false`以外の値を扱うことができます。
大文字小文字などどのケースでも`"true"`または`"t"`、`"1"`、および数字の`1`はすべて`true`になり、それ以外は`false`になります。

<!--
Transforms are not required. If you do not specify a transform name
Ember Data will do no additional processing of the value.
-->

トランスフォームは必須ではありません。
トランスフォーム名が指定されていない場合は、Ember Dataは値の追加処理を行いません。

<!--

#### Custom Transforms

-->

#### カスタムトランスフォーム

<!--
You can also create custom transforms with Ember CLI's `transform` generator:
-->

Ember CLIの`transform`ジェネレータを使用してカスタムトランスフォームを作成できます。

```bash
ember generate transform dollars
```

<!--
Here is a simple transform that converts values between cents and US dollars.
-->

以下はセントと米ドルを変換する単純な例です。

```app/transforms/dollars.js
import DS from 'ember-data';

export default DS.Transform.extend({
  deserialize(serialized) {
    return serialized / 100; // returns dollars
  },

  serialize(deserialized) {
    return deserialized * 100; // returns cents
  }
});
```

<!--
A transform has two functions: `serialize` and `deserialize`. Deserialization
converts a value to a format that the client expects. Serialization does the
reverse and converts a value to the format expected by the persistence layer.
-->

トランスフォームには、`serialize`と`deserialize`という2つの関数があります。
デシリアライズは、値をクライアントが期待する形式に変換します。
シリアライズはその逆を行い、値を永続性レイヤー(サーバー)が期待する形式に変換します。

<!--
You would use the custom `dollars` transform like this:
-->

上記のカスタムトランスフォームの`dollars`を使うには以下のように指定します。

```app/models/product.js
import DS from 'ember-data';

export default DS.Model.extend({
  spent: DS.attr('dollars')
});
```

<!--
### Options
-->

### オプション

<!--
`DS.attr` can also take a hash of options as a second parameter. At the moment
the only option available is `defaultValue`, which can use a value or a function
to set the default value of the attribute if one is not supplied.
-->

`DS.attr`は第2引数にハッシュを受け取ることもできます。
現時点では、使用可能なオプションは`defaultValue`だけです。
defaultValueは、値または関数を使用してアトリビュートのデフォルト値を設定できます。

<!--
In the following example we define that `verified` has a default value of
`false` and `createdAt` defaults to the current date at the time of the model's
creation:
-->

次の例では、`verified`はデフォルト値が`false`で、`createdAt`のデフォルト値はモデルの作成時の日付が設定されます。

```app/models/user.js
import DS from 'ember-data';

export default DS.Model.extend({
  username: DS.attr('string'),
  email: DS.attr('string'),
  verified: DS.attr('boolean', { defaultValue: false }),
  createdAt: DS.attr('date', {
    defaultValue() { return new Date(); }
  })
});
```
