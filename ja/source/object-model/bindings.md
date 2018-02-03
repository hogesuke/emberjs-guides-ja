<!--
Unlike most other frameworks that include some sort of binding implementation,
bindings in Ember.js can be used with any object. That said, bindings are most
often used within the Ember framework itself, and for most problems Ember app
developers face, computed properties are the appropriate solution.
-->

何らかのバインディング実装を含む他のほとんどのフレームワークとは異なり、Ember.jsのバインディングはどのオブジェクトでも使用できます。
つまり、バインディングはEmberフレームワーク自体で最も頻繁に使用され、Emberアプリケーション開発者が直面するほとんどの問題は、算出プロパティが適切に解決できます。

<!--
The easiest way to create a two-way binding is to use a [`computed.alias()`](https://www.emberjs.com/api/ember/2.16/classes/@ember%2Fobject%2Fcomputed/methods/alias?anchor=alias&show=inherited%2Cprotected%2Cprivate%2Cdeprecated),
that specifies the path to another object.
-->

双方向バインディングを作成する最も簡単な方法は、別のオブジェクトへのパスを指定する[`computed.alias()`](https://www.emberjs.com/api/ember/2.16/classes/@ember%2Fobject%2Fcomputed/methods/alias?anchor=alias&show=inherited%2Cprotected%2Cprivate%2Cdeprecated)を使用することです。

```javascript
import EmberObject from '@ember/object';
import { alias } from '@ember/object/computed'

husband = EmberObject.create({
  pets: 0
});

Wife = EmberObject.extend({
  pets: alias('husband.pets')
});

wife = Wife.create({
  husband: husband
});

wife.get('pets'); // 0

// Someone gets a pet.
husband.set('pets', 1);
wife.get('pets'); // 1
```

<!--
Note that bindings don't update immediately. Ember waits until all of your
application code has finished running before synchronizing changes, so you can
change a bound property as many times as you'd like without worrying about the
overhead of syncing bindings when values are transient.
-->

バインディングはすぐに更新されないことに注意してください。
Emberは、変更を同期する前に、すべてのアプリケーションコードの実行が完了するのを待つので、一時的な値でも同期のオーバーヘッドを気にせずに、バインドされたプロパティを何回でも変更できます。

<!--
## One-Way Bindings
-->

## 単方向バインディング

<!--
A one-way binding only propagates changes in one direction, using
[`computed.oneWay()`](https://www.emberjs.com/api/ember/2.16/classes/@ember%2Fobject%2Fcomputed/methods/alias?anchor=oneWay&show=inherited%2Cprotected%2Cprivate%2Cdeprecated). Often, one-way bindings are a performance
optimization and you can safely use a two-way binding (which are de facto one-way bindings if you only ever change one side).
Sometimes one-way bindings are useful to achieve specific behaviour such as a
default that is the same as another property but can be overridden (e.g. a
shipping address that starts the same as a billing address but can later be
changed)
-->

単方向バインディングは、[`computed.oneWay()`](https://www.emberjs.com/api/ember/2.16/classes/@ember%2Fobject%2Fcomputed/methods/alias?anchor=oneWay&show=inherited%2Cprotected%2Cprivate%2Cdeprecated)を使用して変更を一方向に伝播します。
多くの場合、単方向バインディングはパフォーマンスの最適化であり、片方だけ変更した場合は事実上単方向バインディングである双方向バインディングを安全に使用できます。
単方向バインディングは、別のプロパティと同じではあるが上書きできるデフォルト値(例えば、請求先住所と配送先住所は初期値は同じで、後で配送先住所だけ変更できる)などの特定の動作を達成するのに便利です。

<!--
```javascript
import EmberObject, { computed } from '@ember/object';
import Component from '@ember/component';
import { oneWay } from '@ember/object/computed'

user = EmberObject.create({
  fullName: 'Kara Gates'
});

UserComponent = Component.extend({
  userName: oneWay('user.fullName')
});

userComponent = UserComponent.create({
  user: user
});

// Changing the name of the user object changes
// the value on the view.
user.set('fullName', 'Krang Gates');
// userComponent.userName will become "Krang Gates"

// ...but changes to the view don't make it back to
// the object.
userComponent.set('userName', 'Truckasaurus Gates');
user.get('fullName'); // "Krang Gates"
```
-->

```javascript
import EmberObject, { computed } from '@ember/object';
import Component from '@ember/component';
import { oneWay } from '@ember/object/computed'

user = EmberObject.create({
  fullName: 'Kara Gates'
});

UserComponent = Component.extend({
  userName: oneWay('user.fullName')
});

userComponent = UserComponent.create({
  user: user
});

// ユーザーオブジェクトの名前を変更すると、ビューの値が変更されます。
user.set('fullName', 'Krang Gates');
// userComponent.userNameは "Krang Gates"になります。

// ...ビューに変更を加えても、参照しているuserオブジェクトは変更されません。
userComponent.set('userName', 'Truckasaurus Gates');
user.get('fullName'); // "Krang Gates"
```
