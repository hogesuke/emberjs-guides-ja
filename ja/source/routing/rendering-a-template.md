<!--
One job of a route handler is rendering the appropriate template to the screen.
-->

ルートハンドラの仕事の1つは、適切なテンプレートを画面に描画することです。

<!--
By default, a route handler will render the template with the same name as the
route. Take this router:
-->

デフォルトでは、ルートハンドラはルートと同じ名前のテンプレートを描画します。
以下のようにルータを設定した場合、

```app/router.js
Router.map(function() {
  this.route('posts', function() {
    this.route('new');
  });
});
```

<!--
Here, the `posts` route will render the `posts.hbs` template, and
the `posts.new` route will render `posts/new.hbs`.
-->

`posts`ルートは`posts.hbs`テンプレートを描画し、`posts.new`ルートは`posts/new.hbs`を表示します。

<!--
Each template will be rendered into the `{{outlet}}` of its parent route's
template. For example, the `posts.new` route will render its template into the
`posts.hbs`'s `{{outlet}}`, and the `posts` route will render its template into
the `application.hbs`'s `{{outlet}}`.
-->

各テンプレートは、親ルートのテンプレートの`{{outlet}}`に描画されます。
例えば、`posts.new`ルートはそのテンプレートを`posts.hbs`の`{{outlet}}`に描画し、`posts`ルートはそのテンプレートを`application.hbs`の`{{outlet}}`に描画します。

<!--
If you want to render a template other than the default one, set the route's [`templateName`](https://www.emberjs.com/api/ember/2.16/classes/Route/properties/templateName?anchor=templateName) property to the name of
the template you want to render instead.
-->

デフォルト以外のテンプレートを描画したい場合は、ルートの[`templateName`](https://www.emberjs.com/api/ember/2.16/classes/Route/properties/templateName?anchor=templateName)プロパティーに、描画したいテンプレートの名前を指定します。

```app/routes/posts.js
import Route from '@ember/routing/route';

export default Route.extend({
  templateName: 'posts/favorite-posts'
});
```

<!--
You can override the [`renderTemplate()`](https://www.emberjs.com/api/ember/2.16/classes/Route/methods/renderTemplate?anchor=renderTemplate) hook if you want finer control over template rendering.
Among other things, it allows you to choose the controller used to configure the template and specific outlet to render it into.
-->

描画を細かく制御したい場合は、[`renderTemplate()`](https://www.emberjs.com/api/ember/2.16/classes/Route/methods/renderTemplate?anchor=renderTemplate)フックをオーバーライドします。
テンプレートで使用するコントローラと、どの`{{outlet}}`に描画するかを指定することができます。
