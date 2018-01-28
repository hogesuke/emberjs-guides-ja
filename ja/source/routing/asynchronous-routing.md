<!--
This section covers some more advanced features of the router and its
capability for handling complex async logic within your app.
-->

このセクションでは、ルータのより高度な機能と、アプリ内で複雑な非同期ロジックを処理する機能について説明します。

<!--
### A Word on Promises...
-->

### 約束の言葉...

<!--
Ember's approach to handling asynchronous logic in the router makes
heavy use of the concept of Promises. In short, promises are objects that
represent an eventual value. A promise can either _fulfill_
(successfully resolve the value) or _reject_ (fail to resolve the
value). The way to retrieve this eventual value, or handle the cases
when the promise rejects, is via the promise's [`then()`](https://www.emberjs.com/api/ember/2.16/classes/Promise/methods/then?anchor=then) method, which
accepts two optional callbacks, one for fulfillment and one for
rejection. If the promise fulfills, the fulfillment handler gets called
with the fulfilled value as its sole argument, and if the promise rejects,
the rejection handler gets called with a reason for the rejection as its
sole argument. For example:
-->

ルータで非同期ロジックを扱う際、EmberではPromisesという概念を大いに活用しています。
手短に言えば、Promiseは最終的な値を表すオブジェクトです。
Promiseは、成功(値をうまく解決)するか、拒否する(値の解決に失敗)ことができます。
この最終的な値を取得するには、成功時と拒否時(オプション)の2つのコールバックを受け取るpromiseの[then()](https://www.emberjs.com/api/ember/2.16/classes/Promise/methods/then?anchor=then)メソッドを使用します。
Promiseが解決されれば、解決時の値がコールバックに渡され、失敗れた場合、エラーオブジェクトだけが引数に渡されます。

例:


```js
let promise = fetchTheAnswer();

promise.then(fulfill, reject);

function fulfill(answer) {
  console.log(`The answer is ${answer}`);
}

function reject(reason) {
  console.log(`Couldn't get the answer! Reason: ${reason}`);
}
```

<!--
Much of the power of promises comes from the fact that they can be
chained together to perform sequential asynchronous operations:
-->

Promiseの良い点は、一連の非同期処理を連鎖できるところです。

```js
// Note: jQuery AJAX methods return promises
let usernamesPromise = Ember.$.getJSON('/usernames.json');

usernamesPromise.then(fetchPhotosOfUsers)
                .then(applyInstagramFilters)
                .then(uploadTrendyPhotoAlbum)
                .then(displaySuccessMessage, handleErrors);
```

<!--
In the above example, if any of the methods
`fetchPhotosOfUsers`, `applyInstagramFilters`, or
`uploadTrendyPhotoAlbum` returns a promise that rejects,
`handleErrors` will be called with
the reason for the failure. In this manner, promises approximate an
asynchronous form of try-catch statements that prevent the rightward
flow of nested callback after nested callback and facilitate a saner
approach to managing complex asynchronous logic in your applications.
-->

上記の例では、`fetchPhotosOfUsers`、`applyInstagramFilters`、または`uploadTrendyPhotoAlbum`のいずれかが失敗した場合、`handleErrors`が失敗の理由とともに呼び出されます。
このように、ネストしたコールバックの後さらにネストを重ねるようなことはせず、try-catch構文のように
アプリケーションで複雑な非同期ロジックを賢く管理することができます。

<!--
This guide doesn't intend to fully delve into all the different ways
promises can be used, but if you'd like a more thorough introduction,
take a look at the readme for [RSVP](https://github.com/tildeio/rsvp.js),
the promise library that Ember uses.
-->

このガイドでは、Promiseの使い方を完全に掘り下げるつもりはありませんので、もっと徹底的に知りたい方は、
Emberで使用されているPromiseについては[RSVP](https://github.com/tildeio/rsvp.js)のreadmeをご覧ください。

<!--
### The Router Pauses for Promises
-->

### ルーターはPromiseを待つ

<!--
When transitioning between routes, the Ember router collects all of the
models (via the `model` hook) that will be passed to the route's
controllers at the end of the transition. If the `model` hook (or the related
`beforeModel` or `afterModel` hooks) return normal (non-promise) objects or
arrays, the transition will complete immediately. But if the `model` hook
(or the related `beforeModel` or `afterModel` hooks) returns a promise (or
if a promise was provided as an argument to `transitionTo`), the transition
will pause until that promise fulfills or rejects.
-->

ルート間を移動(トランジション)する時、Emberルーターは、トランジションの終わりに(`model`フックを介して)すべてのモデルを収集し、ルートのコントローラに渡します。 
`model`フック（または`beforeModel`、`afterModel`フック）が通常の(Promiseではない)
オブジェクトか配列を返す場合、そのトランジションは直ちに完了します。
しかし、`model`フック(か`beforeModel`か`afterModel`フック)がPromiseを返す場合、
(または、Promiseが`transitionTo`の引数に渡された場合)、そのPromiseが解決するか失敗するまで、トランジションは一時停止されます。

<!--
The router considers any object with a `then()` method
defined on it to be a promise.
-->

ルータは `then()`メソッドを持つオブジェクトをすべてPromiseと見なします。

<!--
If the promise fulfills, the transition will pick up where it left off and
begin resolving the next (child) route's model, pausing if it too is a
promise, and so on, until all destination route models have been
resolved. The values passed to the [`setupController()`](https://www.emberjs.com/api/ember/2.16/classes/Route/methods/setupController?anchor=setupController) hook for each route
will be the fulfilled values from the promises.
-->

Promiseが解決されば、トランジションは中断されたところから再開します。
そして、次の(子)ルートのモデルの解決を開始し、それがPromiseであれば一時停止するのを、
目的地までのルートのモデルが全て解決されるまで繰り返します。
各ルートの[`setupController()`](https://www.emberjs.com/api/ember/2.16/classes/Route/methods/setupController?anchor=setupController)フックに渡される値は、Promiseから得られた値です。

<!--
A basic example:
-->

基本的な例:


```app/routes/tardy.js
import Ember from 'ember';
import RSVP from 'rsvp';
import { later } from '@ember/runloop';

export default Route.extend({
  model() {
    return new RSVP.Promise(function(resolve) {
      later(function() {
        resolve({ msg: 'Hold Your Horses' });
      }, 3000);
    });
  },

  setupController(controller, model) {
    console.log(model.msg); // "Hold Your Horses"
  }
});
```

<!--
When transitioning into `route:tardy`, the `model()` hook will be called and
return a promise that won't resolve until 3 seconds later, during which time
the router will be paused in mid-transition. When the promise eventually
fulfills, the router will continue transitioning and eventually call
`route:tardy`'s `setupController()` hook with the resolved object.
-->

`route:tardy`に移動すると、`model()`フックが呼び出され、3秒後まで解決されない約束が返されます。
その間、ルータは移行の途中で一時停止します。
Promiseが最終的に解決されると、ルータはトランジションを継続し、解決されたオブジェクトを最終的に`route:tardy`の`setupController()`フックに渡して呼びます。

<!--
This pause-on-promise behavior is extremely valuable for when you need
to guarantee that a route's data has fully loaded before displaying a
new template.
-->

この`pause-on-promise(Promiseで一時停止)の動作は、新たにテンプレートが表示される前にルートのデータが完全にロードされていることを保証する必要があるケースで、非常に役立ちます。

<!--
### When Promises Reject...
-->

### Promiseがrejectされた時...

<!--
We've covered the case when a model promise fulfills, but what if it rejects?
-->

モデルのPromiseが解決した場合については説明しましたが、失敗(reject)した場合はどうなるでしょうか？

<!--
By default, if a model promise rejects during a transition, the transition is
aborted, no new destination route templates are rendered, and an error
is logged to the console.
-->

デフォルトでは、移行中にモデルのPromiseが失敗された場合、トランジションは中止され、目的のルートのテンプレートは描画されず、
コンソールにエラーログが出ます。

<!--
You can configure this error-handling logic via the `error` handler on
the route's `actions` hash. When a promise rejects, an `error` event
will be fired on that route and bubble up to `route:application`'s
default error handler unless it is handled by a custom error handler
along the way, e.g.:
-->

エラーを適切に処理したい場合は、ルートの`actions`ハッシュに`error`を実装します。
Promiseが失敗すると、そのルートで`error`イベントが発生し、途中に`error`の実装がない限り、
`route:application`のデフォルトのエラーハンドラに伝播します。

例:

<!--
```app/routes/good-for-nothing.js
import Route from '@ember/routing/route';
import RSVP from 'rsvp';

export default Route.extend({
  model() {
    return RSVP.reject("FAIL");
  },

  actions: {
    error(reason) {
      alert(reason); // "FAIL"

      // Can transition to another route here, e.g.
      // this.transitionTo('index');

      // Uncomment the line below to bubble this error event:
      // return true;
    }
  }
});
```
-->

```app/routes/good-for-nothing.js
import Route from '@ember/routing/route';
import RSVP from 'rsvp';

export default Route.extend({
  model() {
    return RSVP.reject("FAIL");
  },

  actions: {
    error(reason) {
      alert(reason); // "失敗"

      // 以下のように他のルートに飛ばすこともできます
      // this.transitionTo('index');

      // errorイベントを祖先ルートに伝播したい場合は、
      // コメントを外してtrueを返してください。
      // return true;
    }
  }
});
```

<!--
In the above example, the error event would stop right at
`route:good-for-nothing`'s error handler and not continue to bubble. To
make the event continue bubbling up to `route:application`, you can
return true from the error handler.
-->

上の例では、エラーイベントは`route:good-for-nothing`ルートで終了します。
イベントが`route:application`まで伝播するようにするには、エラーハンドラで`true`を返します。

<!--
### Recovering from Rejection
-->

### Promiseのrejectをカバーする

<!--
Rejected model promises halt transitions, but because promises are chainable,
you can catch promise rejects within the `model` hook itself and convert
them into fulfills that won't halt the transition.
-->

モデルでPromiseが失敗(reject)した時は、トランジションが停止されますが、
Promiseはチェーン(連鎖)できるので、
`model`フック自体の中でPromiseの失敗を捕捉(catch)し、トランンジションを停止させないよう変更できます。

```app/routes/funky.js
import Route from '@ember/routing/route';

export default Route.extend({
  model() {
    return iHopeThisWorks().catch(function() {
      // Promise rejected, fulfill with some default value to
      // use as the route's model and continue on with the transition
      return { msg: 'Recovered from rejected promise' };
    });
  }
});
```
