<!--
The Ember Router allows you to provide feedback that a route is loading, as well
as when an error occurs in loading a route.
-->

Ember Routerでは、ルートのロード中や、エラーが発生したことを、ユーザに見せることができます。

<!--
## `loading` substates
-->

## `loading`サブステート

<!--
During the `beforeModel`, `model`, and `afterModel` hooks, data may take some
time to load. Technically, the router pauses the transition until the promises
returned from each hook fulfill.
-->

`beforeModel`、`model`、および`afterModel`フックでは、データが読み込まれるまでに時間がかかることがあります。
厳密には、各フックから返されたPromiseが達成されるまで、ルータはトランジションを一時停止します。

<!--
Consider the following:
-->

次のコードを見てください。

```app/router.js
Router.map(function() {
  this.route('slow-model');
});
```

```app/routes/slow-model.js
import Route from '@ember/routing/router';

export default Route.extend({
  model() {
    return this.get('store').findAll('slow-model');
  }
});
```

<!--
If you navigate to `slow-model`, in the `model` hook,
the query may take a long time to complete.
During this time, your UI isn't really giving you any feedback as to
what's happening. If you're entering this route after a full page
refresh, your UI will be entirely blank, as you have not actually
finished fully entering any route and haven't yet displayed any
templates. If you're navigating to `slow-model` from another
route, you'll continue to see the templates from the previous route
until the model finish loading, and then, boom, suddenly all the
templates for `slow-model` load.
-->

`slow-model`に移動する時に、`model`フックでクエリが完了するまでに時間がかかることがあります。
この間、UIは実際に何が起こっているかについてのフィードバックをユーザに与えていません。
ブラウザの更新でこのルートにアクセスすると、ルートの処理が完全に終わるまではテンプレートが表示されていないため、完全に空白になページが表示されます。
`slow-model`に別のルートから移動すると、移動前のルートが表示され続け、`slow-model`のモデルのロードが完了した途端にドバッと`slow-model`のテンプレートが一気に表示されます。

<!--
So, how can we provide some visual feedback during the transition?
-->

このような場合に、処理中であることをユーザに見せることできます。

<!--
Simply define a template called `loading` (and optionally a corresponding route)
that Ember will transition to. The
intermediate transition into the loading substate happens immediately
(synchronously), the URL won't be updated, and, unlike other transitions, the
currently active transition won't be aborted.
-->

遷移先のルートに`loading`(およびオプションで対応するルート)というテンプレートを定義するだけです。
`loading`サブステートへの変位は直ちに(同期的に)行われ、URLは更新されず、トランジションとは異なり、中止されません。

<!--
Once the main transition into `slow-model` completes, the `loading`
route will be exited and the transition to `slow-model` will continue.
-->

`slow-model`への主トランジションが完了すると、`loading`ルートが終了し、`slow-model`へのトランジションが継続されます。

<!--
For nested routes, like:
-->

以下のようなネストしたルートの場合、

```app/router.js
Router.map(function() {
  this.route('foo', function() {
    this.route('bar', function() {
      this.route('slow-model');
    });
  });
});
```

<!--
When accessing `foo.bar.slow-model` route then Ember will alternate trying to
find a `routeName-loading` or `loading` template in the hierarchy starting with
`foo.bar.slow-model-loading`:
-->

`foo.bar.slow-model`ルートにアクセスすると、Emberは、以下ように、`foo.bar.slow-model-loading`、`routeName-loading`、`loading`という順でテンプレートを探します。

<!--
1. `foo.bar.slow-model-loading`
2. `foo.bar.loading` or `foo.bar-loading`
3. `foo.loading` or `foo-loading`
4. `loading` or `application-loading`
-->

1. `foo.bar.slow-model-loading`
2. `foo.bar.loading`または`foo.bar-loading`
3. `foo.loading`または`foo-loading`
4. `loading`または`application-loading`

<!--
It's important to note that for `slow-model` itself, Ember will not try to
find a `slow-model.loading` template but for the rest of the hierarchy either
syntax is acceptable. This can be useful for creating a custom loading screen
for a leaf route like `slow-model`.
-->

`slow-model`自体にアクセスする場合、Emberは`slow-model`の`loading`テンプレートを見つけようとはしませんが、上の階層では上記のルール通りであることに注意してください。
これは、`slow-model`のような子ルートを持たないルート(葉ルート)でカスタマイズしたロード画面を表示するのに便利です。

<!--
When accessing `foo.bar` route then Ember will search for:
-->

`foo.bar`にアクセスすると、Emberは以下を検索します。

<!--
1. `foo.bar-loading`
2. `foo.loading` or `foo-loading`
3. `loading` or `application-loading`
-->

1. `foo.bar-loading`
2. `foo.loading`または`foo-loading`
3. `loading`または`application-loading`

<!--
It's important to note that `foo.bar.loading` is not considered now.
-->

`foo.bar.loading`は現在考慮されていないことに注意することが重要です。

<!--
### The `loading` event
-->

### `loading`イベント

<!--
If the various `beforeModel`/`model`/`afterModel` hooks
don't immediately resolve, a [`loading`](https://www.emberjs.com/api/ember/2.16/classes/Route/events/loading?anchor=loading) event will be fired on that route.
-->

`beforeModel` / `model` / `afterModel`フックがすぐに解決されない場合、そのルートの[`loading`](https://www.emberjs.com/api/ember/2.16/classes/Route/events/loading?anchor=loading)アクションが呼ばれます。

```app/routes/foo-slow-model.js
import Route from '@ember/routing/route';

export default Route.extend({
  model() {
    return this.get('store').findAll('slow-model');
  },

  actions: {
    loading(transition, originRoute) {
      let controller = this.controllerFor('foo');
      controller.set('currentlyLoading', true);

      return true; // allows the loading template to be shown
    }
  }
});
```

<!--
If the `loading` handler is not defined at the specific route,
the event will continue to bubble above a transition's parent
route, providing the `application` route the opportunity to manage it.
-->

`loading`ハンドラが該当のルートで定義されていない場合、イベントは親ルートに伝達し続け、`application`ルートにそれを管理する機会を与えます。

<!--
When using the `loading` handler, we can make use of the transition promise to know when the loading event is over:
-->

`loading`ハンドラを使用する場合、ローディングイベントが終了したことを知るためには、トランジションのpromiseを利用します。


```app/routes/foo-slow-model.js
import Route from '@ember/routing/route';

export default Route.extend({
  …

  actions: {
    loading(transition, originRoute) {
      let controller = this.controllerFor('foo');
      controller.set('currentlyLoading', true);
      transition.promise.finally(function() {
          controller.set('currentlyLoading', false);
      });
    }
  }
});
```

<!--
In case we want both custom logic and the default behaviour for the loading substate,
we can implement the `loading` action and let it bubble by returning `true`.
-->

`loading`サブステートのカスタムロジックとデフォルトの処理の両方が必要な場合は、`loading`アクションの実装で`true`を返すことで伝播できます。

```app/routes/foo-slow-model.js
import Ember from 'ember';

export default Ember.Route.extend({
  ...
  actions: {
    loading(transition) {
      let start = new Date();
      transition.promise.finally(() => {
        this.get('notifier').notify(`Took ${new Date() - start}ms to load`);
      });

      return true;
    }
  }
});
```

<!--
## `error` substates
-->

## `error`サブステート

<!--
Ember provides an analogous approach to `loading` substates in
the case of errors encountered during a transition.
-->

Emberは、トランジション中にエラーが発生した場合、`loading`サブステートと似たの手段を提供します。

<!--
Similar to how the default `loading` event handlers are implemented,
the default `error` handlers will look for an appropriate error substate to
enter, if one can be found.
-->

`loading`イベントハンドラーの実装方法と同様に、`error`ハンドラーも該当する`error`サブステートがあるか探します。

```app/router.js
Router.map(function() {
  this.route('articles', function() {
    this.route('overview');
  });
});
```

<!--
As with the `loading` substate, on a thrown error or rejected promise returned
from the `articles.overview` route's `model` hook (or `beforeModel` or
`afterModel`) Ember will look for an error template or route in the following
order:
-->

`loading`サブステートと同様に、`articles.overview`ルートの`model`フック(`beforeModel`と`afterModel`を含む）でエラーが起きたり、Promiseがrejectされた場合、Emberは次の順序でエラーテンプレートかルートを探します：

<!--
1. `articles.overview-error`
2. `articles.error` or `articles-error`
3. `error` or `application-error`
-->

1. `articles.overview-error`
2. `articles.error`または`articles-error`
3. `error`または`application-error`

<!--
If one of the above is found, the router will immediately transition into
that substate (without updating the URL). The "reason" for the error
(i.e. the exception thrown or the promise reject value) will be passed
to that error state as its `model`.
-->

上記のいずれかが見つかった場合、ルータはすぐにそのサブステートに移ります。(URLは更新されません)
エラーの理由(すなわち、throwされた例外またはPromiseのリジェクト値)、errorが以下のように`model`として渡されます。

<!--
The model hooks (`beforeModel`, `model`, and `afterModel`) of an error substate
are not called. Only the `setupController` method of the error substate is
called with the `error` as the model. See example below:
-->

エラーサブステートのモデルフック群(`beforeModel`、`model`、および`afterModel`)は呼び出されません。
エラーサブステートの`setupController`メソッドのみがモデルとして`error`とともに呼び出されます。
以下の例を参照してください。

```js
setupController(controller, error) {
  Ember.Logger.debug(error.message);
  this._super(...arguments);
}
```

<!--
If no viable error substates can be found, an error message will be
logged.
-->

実行可能なエラーサブステートが見つからない場合は、エラーメッセージがログに記録されます。

<!--
### The `error` event
-->

### `error`イベント

<!--
If the `articles.overview` route's `model` hook returns a promise that rejects
(for instance the server returned an error, the user isn't logged in,
etc.), an [`error`](https://www.emberjs.com/api/ember/2.16/classes/Route/events/error?anchor=error) event will fire from that route and bubble upward.
This `error` event can be handled and used to display an error message,
redirect to a login page, etc.
-->

`articles.overview`ルートの`model`フックでPromiseがrejctされた場合(例えばサーバからエラーが返されたり、ユーザがログインしていないなど)、[`error`](https://www.emberjs.com/api/ember/2.16/classes/Route/events/error?anchor=error)イベントはそのルートから発生して、親ルートに伝播されます。
`error`イベントは、エラーメッセージの表示、ログインページへのリダイレクトなどに使用できます。

```app/routes/articles-overview.js
import Route from '@ember/routing/route';

export default Route.extend({
  model(params) {
    return this.get('store').findAll('privileged-model');
  },

  actions: {
    error(error, transition) {
      if (error.status === '403') {
        this.replaceWith('login');
      } else {
        // Let the route above this handle the error.
        return true;
      }
    }
  }
});
```

<!--
Analogous to the `loading` event, you could manage the `error` event
at the application level to avoid writing the same code for multiple routes.
-->

`loading`イベントと同様に、アプリケーションレベルで`error`イベントを管理して、複数のルートで同じコードを書かないようにすることができます。

<!--
In case we want to run some custom logic and have the default behaviour of rendering the error template,
we can handle the `error` event and let it bubble by returning `true`.
-->

任意の処理の後に、errorテンプレートを描画するデフォルトの処理が必要な場合は、`error`イベントを実装し、`true`を返して伝達させます。

```app/routes/articles-overview.js
import Ember from 'ember';

export default Ember.Route.extend({
  model(params) {
    return this.get('store').findAll('privileged-model');
  },
  actions: {
    error(error) {
      this.get('notifier').error(error);

      return true;
    }
  }
});
```
