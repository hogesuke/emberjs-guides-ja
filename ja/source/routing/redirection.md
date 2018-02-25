<!--
Sometimes you want to redirect a user to a different page than what they requested for.
-->

場合によっては、ユーザーが要求したものとは異なるページにユーザーをリダイレクトすることがあります。

<!--
For example, if they're not logged in, you might want to prevent them from editing their profile, accessing private information,
or checking out items in their shopping cart.
Usually you want to redirect them to the login page, and after they have successfully logged in, take them back to the page they originally wanted to access.
-->

例えば、ユーザーがログインしていない時に、プロフィールの編集、個人情報へのアクセス、ショッピングカート内のアイテムのチェックアウトを禁止したい時があります。
その場合、ユーザーをログインページにリダイレクトして、ログインに成功した後、最初にアクセスしたページに戻したいと思うでしょう。

<!--
There are many other reasons you probably want to have the last word on whether a user can or cannot access a certain page.
Ember allows you to control that access with a combination of hooks and methods in your route.
-->

他にも、ユーザーが特定のページにアクセスできるかどうかを最終的に判断したい場合が多くあります。
Emberでは、ルートのフックとメソッドの組み合わせでそのアクセスを制御することができます。

<!--
One of the methods is [`transitionTo()`](https://www.emberjs.com/api/ember/release/classes/Route/methods/transitionTo?anchor=transitionTo).
Calling `transitionTo()` from a route or
[`transitionToRoute()`](https://www.emberjs.com/api/ember/release/classes/Controller/methods/transitionToRoute?anchor=transitionToRoute) from a controller will stop any transitions currently in progress and start a new one, functioning as a redirect.
`transitionTo()` behaves exactly like the [link-to](../../templates/links) helper.
-->

そのメソッドの1つは[`transitionTo()`](https://www.emberjs.com/api/ember/release/classes/Route/methods/transitionTo?anchor=transitionTo)です。
コントローラからの[`transitionToRoute()`](https://www.emberjs.com/api/ember/release/classes/Controller/methods/transitionToRoute?anchor=transitionToRoute)、またはルートからの`transitionTo()`の呼び出しは、現在進行中のトランジションを停止し、新しいトランジションを開始し、リダイレクトとして機能します。
`transitionTo()`は、[link-to](../../templates/links)ヘルパーとまったく同じように動作します。

<!--
The other one is [`replaceWith()`](https://www.emberjs.com/api/ember/release/classes/Route/methods/transitionTo?anchor=replaceWith) which works the same way as `transitionTo()`.
The only difference between them is how they manage history.
`replaceWith()` substitutes the current route entry and replaces it with that of the route we are redirecting to,
while `transitionTo()` leaves the entry for the current route and creates a new one for the redirection.
-->

もう1つは、`transitionTo()`と同じように動作する[`replaceWith()`](https://www.emberjs.com/api/ember/release/classes/Route/methods/transitionTo?anchor=replaceWith) です。
これらの唯一の違いは、履歴をどのように管理するかです。
`replaceWith()`は、現在のルートエントリを置換し、リダイレクト先のルートのエントリに置き換えます。
一方、`transitionTo()`は現在のルートのエントリを残し、リダイレクトの新しいエントリを作成します。

<!--
If the new route has dynamic segments, you need to pass either a _model_ or an _identifier_ for each segment.
Passing a model will skip the route's `model()` hook since the model is already loaded.
-->

新しいルートに動的セグメントがある場合、各セグメントにモデルまたは識別子のいずれかを渡す必要があります。
モデルを渡すと、モデルがすでにロードされているので、ルートの`model()`フックはスキップされます。

<!--
## Transitioning Before the Model is Known
-->

## モデルが判明する前のトランジション

<!--
Since a route's [`beforeModel()`](https://www.emberjs.com/api/ember/release/classes/Route/methods/transitionTo?anchor=beforeModel) executes before the `model()` hook,
it's a good place to do a redirect if you don't need any information that is contained in the model.
-->

ルートの[`beforeModel()`](https://www.emberjs.com/api/ember/release/classes/Route/methods/transitionTo?anchor=beforeModel)は`model()`フックの前に実行されるため、モデルに含まれている情報が必要ない場合は、ここでリダイレクトするのがよいでしょう。

```app/router.js
Router.map(function() {
  this.route('posts');
});
```

```app/routes/index.js
import Route from '@ember/routing/route';

export default Route.extend({
  beforeModel(/* transition */) {
    this.transitionTo('posts'); // Implicitly aborts the on-going transition.
  }
});
```

<!--
`beforeModel()` receives the current transition as an argument, which we can store and retry later.
This allows us to return the user back to the original route.
For example, we might redirect a user to the login page when they try to edit their profile, and immediately redirect
them back to the edit page once they have successfully logged in.
See [Storing and Retrying a Transition](../preventing-and-retrying-transitions/#toc_storing-and-retrying-a-transition)
for how to do that.
-->

`beforeModel()`は引数として現在のトランジションを受け取ります。
トランジションは格納して後で再試行できます。 
これにより、ユーザーを元のルートに戻すことができます。
例えば、ユーザーがプロフィールを編集しようとするとログインページにリダイレクトされ、正常にログインした後すぐに編集ページにリダイレクトできます。
その方法については、 [トランジションの保存と再実行](../preventing-and-retrying-transitions/#toc_トランジションの保存と再実行)を参照してください。

<!--
If you need to examine some application state to figure out where to redirect,
you might use a [service](../../applications/services).
-->

リダイレクトする場所を特定するためにアプリケーションの状態を調べる必要がある場合は、[サービス](../../applications/services)を使用することができます。

<!--
## Transitioning After the Model is Known
-->

## モデルが判明した後のトランジション

<!--
If you need information about the current model in order to decide about redirection, you can use the [`afterModel()`](https://www.emberjs.com/api/ember/release/classes/Route/methods/transitionTo?anchor=afterModel) hook.
It receives the resolved model as the first parameter and the transition as the second one.
For example:
-->

リダイレクトするかの判断のために、モデルに関する情報が必要な場合は、[`afterModel()`](https://www.emberjs.com/api/ember/release/classes/Route/methods/transitionTo?anchor=afterModel)フックを使用します。
第1引数にPromiseが解決したモデルを受け取り、第2引数にトランジションを受け取ります。
例えば、下記の場合、

```app/router.js
Router.map(function() {
  this.route('posts');
  this.route('post', { path: '/post/:post_id' });
});
```

```app/routes/posts.js
import Route from '@ember/routing/route';

export default Route.extend({
  afterModel(model, transition) {
    if (model.get('length') === 1) {
      this.transitionTo('post', model.get('firstObject'));
    }
  }
});
```

<!--
When transitioning to the `posts` route if it turns out that there is only one post,
the current transition will be aborted in favor of redirecting to the `PostRoute`
with the single post object being its model.
-->

`posts`ルートに移動した際、`posts`が1つしかないことが判明したら、現在のトランジションを中止し`post`ルートにリダイレクトし、その1件のpostオブジェクトをモデルにしています。

<!--
### Child Routes
-->

### 子ルートへのリダイレクション
<!--
Let's change the router above to use a nested route, like this:
-->

上記のルーターを次のようにネストしたルートを使用するように変更しましょう。

```app/router.js
Router.map(function() {
  this.route('posts', function() {
    this.route('post', { path: '/:post_id' });
  });
});
```

<!--
If we redirect to `posts.post` in the `afterModel` hook, `afterModel`
essentially invalidates the current attempt to enter this route. So the `posts`
route's `beforeModel`, `model`, and `afterModel` hooks will fire again within
the new, redirected transition. This is inefficient, since they just fired
before the redirect.
-->

`afterModel`フックで`posts.post`にリダイレクトすると、`afterModel`はこのルートに入る現在のトランジションを無効にします。
したがって、`posts`ルート(親ルート)の`beforeModel`、`model`、および`afterModel`フックは、新しいリダイレクトされたトランジション内(子ルート)で再び発動されます。
これはリダイレクトの前にも一度発動しているため効率が悪いです。

<!--
Instead, we can use the [`redirect()`](https://www.emberjs.com/api/ember/release/classes/Route/methods/transitionTo?anchor=redirect) method, which will leave the original
transition validated, and not cause the parent route's hooks to fire again:
-->

そこで、[`redirect()`](https://www.emberjs.com/api/ember/release/classes/Route/methods/transitionTo?anchor=redirect)メソッドを使います。
このメソッドでは、元のトランジションは有効なままなので、親ルートのフックは再び発動されません。

```app/routes/posts.js
import Route from '@ember/routing/route';

export default Route.extend({
  redirect(model, transition) {
    if (model.get('length') === 1) {
      this.transitionTo('posts.post', model.get('firstObject'));
    }
  }
});
```
