<!--
During a route transition, the Ember Router passes a transition
object to the various hooks on the routes involved in the transition.
Any hook that has access to this transition object has the ability
to immediately abort the transition by calling `transition.abort()`,
and if the transition object is stored, it can be re-attempted at a
later time by calling `transition.retry()`.
-->

ルートに遷移する間、Ember Routerは、transitionオブジェクトをトランジションに関係するルート上のさまざまなフックに渡します。
transitionオブジェクトにアクセスできるフックは、`transition.abort()`を呼び出すことによってトランジションを直ちに中止する機能を持ち、transitionオブジェクトが格納されている場合は、後で`transition.retry()`を呼び出すことで再実行できます。

<!--
### Preventing Transitions via `willTransition`
-->

### `willTransition`でのトランジションの中止

<!--
When a transition is attempted, whether via `{{link-to}}`, `transitionTo`,
or a URL change, a `willTransition` action is fired on the currently
active routes. This gives each active route, starting with the leaf-most
route, the opportunity to decide whether or not the transition should occur.
-->

 `{{link-to}}`、`transitionTo`、またはURLの変更を経てトランジションが試行されると、アクティブルートで`willTransition`アクションが呼ばれます。
 これにより、一番端のルートから順に、各アクティブルートへ、その遷移が発生するかどうかを判断する機会が与えられます。

<!--
Imagine your app is in a route that's displaying a complex form for the user
to fill out and the user accidentally navigates backwards. Unless the
transition is prevented, the user might lose all of the progress they
made on the form, which can make for a pretty frustrating user experience.
-->

複雑なフォームを表示しているルートにあり、ユーザが誤って前のページに戻るところを想像してください。
そのトランジションを中止しないと、ユーザーはフォーム上に入力したデータを失う可能性があり、利便性を損なう可能性があります。

<!--
Here's one way this situation could be handled:
-->

この状況を処理する方法は次のとおりです。

<!--
```app/routes/form.js
import Route from '@ember/routing/route';

export default Route.extend({
  actions: {
    willTransition(transition) {
      if (this.controller.get('userHasEnteredData') &&
          !confirm('Are you sure you want to abandon progress?')) {
        transition.abort();
      } else {
        // Bubble the `willTransition` action so that
        // parent routes can decide whether or not to abort.
        return true;
      }
    }
  }
});
```
-->

```app/routes/form.js
import Route from '@ember/routing/route';

export default Route.extend({
  actions: {
    willTransition(transition) {
      if (this.controller.get('userHasEnteredData') &&
          !confirm('この変更を破棄してに次の画面に移りますか？')) {
        transition.abort();
      } else {
        // `willTransition`をバブリングして、親ルートでもトランジション
        // を中止するかどうか判断できるようにしている
        return true;
      }
    }
  }
});
```

<!--
When the user clicks on a `{{link-to}}` helper, or when the app initiates a
transition by using `transitionTo`, the transition will be aborted and the URL
will remain unchanged. However, if the browser back button is used to
navigate away from `route:form`, or if the user manually changes the URL, the
new URL will be navigated to before the `willTransition` action is
called. This will result in the browser displaying the new URL, even if
`willTransition` calls `transition.abort()`.
-->

ユーザーが`{{link-to}}`ヘルパーをクリックするか、`transitionTo`を使用してアプリが移行を開始すると、トランジションは中止され、URLは変更されないままです。
ただし、ブラウザの戻るボタンを使用して`route:form`から移動するか、ユーザーが手動でURLを変更した場合は、`willTransition`アクションが呼び出される前に新しいURLにナビゲートされます。
これにより`willTransition`が`transition.abort()`を呼び出しても、ブラウザに新しいURLが表示されます。

<!--
### Aborting Transitions Within `model`, `beforeModel`, `afterModel`
-->

### `model` `beforeModel` `afterModel` でのトランジションの中止

<!--
The `model`, `beforeModel`, and `afterModel` hooks described in
[Asynchronous Routing](../asynchronous-routing)
each get called with a transition object. This makes it possible for
destination routes to abort attempted transitions.
-->

[非同期ルーティング](../asynchronous-routing)で説明されている`model`、`beforeModel`、および`afterModel`フックは、それぞれtransitionオブジェクトが渡されます。
これにより、移り先のルートでトランジションを中止できます。

```app/routes/disco.js
import Route from '@ember/routing/route';

export default Route.extend({
  beforeModel(transition) {
    if (new Date() > new Date('January 1, 1980')) {
      alert('Sorry, you need a time machine to enter this route.');
      transition.abort();
    }
  }
});
```

<!--
### Storing and Retrying a Transition
-->

### トランジションの保存と再実行

<!--
Aborted transitions can be retried at a later time. A common use case
for this is having an authenticated route redirect the user to a login
page, and then redirecting them back to the authenticated route once
they've logged in.
-->

中止されたトランジションは後で再試行できます。
よくある例だと、認証が必要なルートがユーザをログインページにリダイレクトしてから、ログイン後に元いたルートへのリダイレクトです。

```app/routes/some-authenticated.js
import Route from '@ember/routing/route';

export default Route.extend({
  beforeModel(transition) {
    if (!this.controllerFor('auth').get('userIsLoggedIn')) {
      let loginController = this.controllerFor('login');
      loginController.set('previousTransition', transition);
      this.transitionTo('login');
    }
  }
});
```

<!--
```app/controllers/login.js
import Controller from '@ember/controller';

export default Controller.extend({
  actions: {
    login() {
      // Log the user in, then reattempt previous transition if it exists.
      let previousTransition = this.get('previousTransition');
      if (previousTransition) {
        this.set('previousTransition', null);
        previousTransition.retry();
      } else {
        // Default back to homepage
        this.transitionToRoute('index');
      }
    }
  }
});
```
-->

```app/controllers/login.js
import Controller from '@ember/controller';

export default Controller.extend({
  actions: {
    login() {
      // ユーザをログインさせ、ログイン前にアクセスしたルートがあれば、
      // そのルートに飛ばす
      let previousTransition = this.get('previousTransition');
      if (previousTransition) {
        this.set('previousTransition', null);
        previousTransition.retry();
      } else {
        // デフォルトでトップページに飛ばす
        this.transitionToRoute('index');
      }
    }
  }
});
```

