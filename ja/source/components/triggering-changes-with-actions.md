<!--
You can think of a component as a black box of UI functionality.
So far, you've learned how parent components can pass attributes in to a
child component, and how that component can use those attributes from
both JavaScript and its template.
-->

コンポーネントは、UI機能のブラックボックスと考えることができます。
これまで、親コンポーネントから子コンポーネントに属性を渡す方法と、子コンポーネントに渡されたプロパティをJavaScriptとテンプレートで使う方法を学びました。

<!--
But what about the opposite direction? How does data flow back out of
the component to the parent? In Ember, components use **actions** to
communicate events and changes.
-->

しかし、反対方向はどうでしょうか？
データを子コンポーネントから親コンポーネントに逆に流すには？
Emberでは、コンポーネントは**アクション**を使用してイベントや変更を伝えます。

<!--
Let's look at a simple example of how a component can use an action to
communicate with its parent.
-->

コンポーネントでアクション使って親コンポーネントとやりとりする簡単な例を見てみましょう。

<!--
Imagine we're building an application where users can have accounts. We
need to build the UI for users to delete their account. Because we don't
want users to accidentally delete their accounts, we'll build a button
that requires the user to confirm in order to trigger some
action.
-->

ユーザーアカウントがあるアプリケーションを開発しているところを想像してください。
ユーザーがアカウントを削除するためのUIを構築する必要があります。
ユーザーが誤ってアカウントを削除しないようにするため、ユーザーに確認を要求するボタンを作成します。

<!--
Once we create this "button with confirmation"
component, we want to be able to reuse it all over our application.
-->

この「確認ボタン」コンポーネントを作成すると、アプリケーション全体に再利用できるようになります。

<!--
## Creating the Component
-->

## コンポーネントの作成

<!--
Let's call our component `button-with-confirmation`. We can create it by
typing:
-->

`button-with-confirmation`コンポーネントと呼ぶことにしましょう。
これを作成するには、次のように入力します。

```shell
ember generate component button-with-confirmation
```

<!--
We'll plan to use the component in a template something like this:
-->

このコンポーネントをテンプレートで次のように使用します。

```app/templates/components/user-profile.hbs
{{button-with-confirmation
  text="Click OK to delete your account."
}}
```

<!--
We'll also want to use the component elsewhere, perhaps like this:
-->

また、次のように、このコンポーネントを別の場所でも使用したいと考えています。

```app/templates/components/send-message.hbs
{{button-with-confirmation
  text="Click OK to send your message."
}}
```

<!--
## Designing the Action
-->

## アクションの設計

<!--
When implementing an action on a component that will be handled outside the component, you need to break it down into two steps:
-->

コンポーネントの外部で処理されるアクションをコンポーネントに実装する場合は、次の2つの手順に分けてください。

<!--
1. In the parent component, decide how you want to react to the action.
   Here, we want to have the action delete the user's account when it's used in one place, and
   send a message when used in another place.
2. In the component, determine when something has happened, and when to tell the
   outside world. Here, we want to trigger the outside action (deleting the
   account or sending the message) after the user clicks the button and then
   confirms.
-->

1. 親コンポーネントで、アクションにどのように反応させるかを決める。
   今の例では、ある個所ではユーザーのアカウントを削除し、
   別の個所ではメッセージを送信したいと考えています。
2. 子コンポーネントでは、何かが起こったことを判定し、それをいつ外界に伝えるかを決める。
   今の例では、ユーザーがボタンをクリックして確認した後で外部アクション(アカウント削除、またはメッセージ送信)を実行します。

<!--
Let's take it step by step.
-->

各ステップごとにやっていきましょう。

<!--
## Implementing the Action
-->

## アクションの実装

<!--
In the parent component, let's first define what we want to happen when the
user clicks the button and then confirms. In the first case, we'll find the user's
account and delete it.
-->

親コンポーネントでは、まず、ユーザーがボタンをクリックして確認したときに何をするかを定義します。
まず一つ目は、ユーザーのアカウントを見つけて削除することです。

<!--
In Ember, each component can
have a property called `actions`, where you put functions that can be
[invoked by the user interacting with the component
itself](../../templates/actions/), or by child components.
-->

Emberでは、各コンポーネントに`action`というプロパティを設定できます。
このプロパティには、[ユーザーの操作によってコンポーネント自身とやりとりする](../../templates/actions/)関数か子コンポーネントから呼び出される関数を配置します。

<!--
Let's look at the parent component's JavaScript file. In this example,
imagine we have a parent component called `user-profile` that shows the
user's profile to them.
-->

親コンポーネントのJavaScriptファイルを見てみましょう。
この例では、ユーザーのプロフィールを表示する`user-profile`という親コンポーネントがあるとします。

<!--
We'll implement an action on the parent component called
`userDidDeleteAccount()` that, when called, gets a hypothetical `login`
[service](../../applications/services/) and calls the service's
`deleteUser()` method.
-->

`userDidDeleteAccount()`というアクションを親コンポーネントに実装します。
このアクションが呼び出されると、仮想的な`login`[サービス](../../applications/services/)を取得し、そのサービスの`deleteUser()`メソッドを呼び出します。

```app/components/user-profile.js
import Component from '@ember/component';
import { inject as service } from '@ember/service';

export default Component.extend({
  login: service(),

  actions: {
    userDidDeleteAccount() {
      this.get('login').deleteUser();
    }
  }
});
```

<!--
Now we've implemented our action, but we have not told Ember when we
want this action to be triggered, which is the next step.
-->

さて、アクションは実装しましたが、このアクションをいつ実行するをまだEmberに指示していませんね。
それが次のステップです。

<!--
## Designing the Child Component
-->

## 子コンポーネントの設計

<!--
Next,
in the child component we will implement the logic to confirm that the user wants to take the action they indicated by clicking the button:
-->

次に、子コンポーネントで、ユーザーがボタンをクリックしてアクションの続行を望んでいるのかを確認するロジックを実装します。

```app/components/button-with-confirmation.js
import Component from '@ember/component';

export default Component.extend({
  actions: {
    launchConfirmDialog() {
      this.set('confirmShown', true);
    },

    submitConfirm() {
      // trigger action on parent component
      this.set('confirmShown', false);
    },

    cancelConfirm() {
      this.set('confirmShown', false);
    }
  }
});
```

<!--
The component template will have a button and a div that shows the confirmation dialog
based on the value of `confirmShown`.
-->

コンポーネントテンプレートには、`confirmShown`の値に基づいて確認ダイアログを表示するボタンとdivがあります。

```app/templates/components/button-with-confirmation.hbs
<button {{action "launchConfirmDialog"}}>{{text}}</button>
{{#if confirmShown}}
  <div class="confirm-dialog">
    <button class="confirm-submit" {{action "submitConfirm"}}>OK</button>
    <button class="confirm-cancel" {{action "cancelConfirm"}}>Cancel</button>
  </div>
{{/if}}
```

<!--
## Passing the Action to the Component
-->

## アクションをコンポーネントに渡す

<!--
Now we need to make it so that the `userDidDeleteAccount()` action defined in the parent component `user-profile` can be triggered from within `button-with-confirmation`.
We'll do this by passing the action to the child component in exactly the same way that we pass other properties.
This is possible since actions are simply functions, just like any other method on a component,
and they can therefore be passed from one component to another like this:
-->

次は、親コンポーネントである`user-profile`に定義されテイル`userDidDeleteAccount()`アクションを、
子コンポーネントの`button-with-confirmation`から実行できるようにします。
これを行うには、他のプロパティを渡すのとまったく同じ方法で、子コンポーネントにアクションを渡します。
なぜ渡すことができるかというと、アクションがコンポーネントの他のメソッドと同様に単なる関数だからです。
そのため、コンポーネントから別のコンポーネントへと、以下のように渡すことができます。

```app/templates/components/user-profile.hbs
{{button-with-confirmation
  text="Click here to delete your account."
  onConfirm=(action "userDidDeleteAccount")
}}
```

<!--
This snippet says "take the `userDidDeleteAccount` action from the parent and make it available on the child component as the property `onConfirm`."
Note the use here of the `action` helper,
which serves to return the function named `"userDidDeleteAccount"` that we are passing to the component.
-->

上記のコードには「親から`userDidDeleteAccount`アクションを取得し、それを子コンポーネントの`onConfirm`というプロパティで利用可能にする」というのが書かれています。
ここでの`action`ヘルパーの使い方に注意してください。
これは、`"userDidDeleteAccount"`という名前の関数を返す働きをし、それが返す値をコンポーネントに渡しています。

<!--
We can do a similar thing for our `send-message` component:
-->

`send-message`コンポーネントでも同様のことができます。

```app/templates/components/send-message.hbs
{{button-with-confirmation
  text="Click to send your message."
  onConfirm=(action "sendMessage")
}}
```

<!--
Now, we can use `onConfirm` in the child component to invoke the action on the
parent:
-->

そして、子コンポーネントで`onConfirm`を使って親コンポーネントに対してアクションを呼び出すことができます。

```app/components/button-with-confirmation.js
import Component from '@ember/component';

export default Component.extend({
  actions: {
    launchConfirmDialog() {
      this.set('confirmShown', true);
    },

    submitConfirm() {
      //call the onConfirm property to invoke the passed in action
      this.get('onConfirm')();
    },

    cancelConfirm() {
      this.set('confirmShown', false);
    }
  }
});
```

<!--
`this.get('onConfirm')` will return the function passed from the parent as the
value of `onConfirm`, and the following `()` will invoke the function.
-->

`this.get('onConfirm')`は、親から渡された関数を`onConfirm`の値として返し、次の`()`がその関数を呼び出します。

<!--
Like normal attributes, actions can be a property on the component; the
only difference is that the property is set to a function that knows how
to trigger behavior.
-->

通常の属性と同様に、アクションはコンポーネントのプロパティにも設定できます。
唯一の違いは、プロパティには動作をトリガする方法を知っている関数を設定することです。

<!--
That makes it easy to remember how to add an action to a component. It's
like passing an attribute, but you use the `action` helper to pass
a function instead.
-->

これにより、コンポーネントにアクションを追加する方法を覚えやすくなります。
属性を渡すような感じで、`action`ヘルパーを使用して代わりに関数を渡します。

<!--
Actions in components allow you to decouple an event happening from how it's handled, leading to modular,
more reusable components.
-->

コンポーネントのアクションは、発生したイベントをどのように処理するかを切り離すことができ、モジュール化され、再利用可能なコンポーネントへ導いてくれます。

<!--
## Handling Action Completion
-->

## アクション完了の処理

<!--
Often actions perform asynchronous tasks, such as making an ajax request to a server.
Since actions are functions that can be passed in by a parent component, they are able to return values when called.
The most common scenario is for an action to return a promise so that the component can handle the action's completion.
-->

多くの場合、アクションはサーバへのajaxリクエストといった非同期処理を行います。
アクションは親コンポーネントから渡される関数でもあるため、呼び出された時に値を返すことができます。
最も一般的なシナリオは、コンポーネントがアクションの完了を処理できるようにアクションからPromiseを返すことです。

<!--
In our user `button-with-confirmation` component we want to leave the confirmation modal open until we know that the
operation has completed successfully.
This is accomplished by expecting a promise to be returned from `onConfirm`.
Upon resolution of the promise, we set a property used to indicate the visibility of the confirmation modal.
-->

`button-with-confirmation`コンポーネントでは、操作が正常に完了したことがわかるまで、確認モーダルを開いたままにします。
これは`onConfirm`がPromiseを返すことで実現できます。
Promiseが解決した時に、確認モーダルの表示に使っているプロパティを変更します。


```app/components/button-with-confirmation.js
import Component from '@ember/component';

export default Component.extend({
  actions: {
    launchConfirmDialog() {
      this.set('confirmShown', true);
    },

    submitConfirm() {
      // call onConfirm with the value of the input field as an argument
      let promise = this.get('onConfirm')();
      promise.then(() => {
        this.set('confirmShown', false);
      });
    },

    cancelConfirm() {
      this.set('confirmShown', false);
    }
  }
});
```

<!--
## Passing Arguments
-->

## 引き数を渡す

<!--
Sometimes the parent component invoking an action has some context needed for the action that the child component
doesn't.
Consider, for example,
the case where the `button-with-confirmation` component we've defined is used within `send-message`.
The `sendMessage` action that we pass to the child component may expect a message type parameter to be provided as an argument:
-->

親コンポーネントのアクションの呼び出しに必要なコンテキスト(引数)があり、子コンポーネントがそれを持っていない時があります。
例えば、先ほど定義した`button-with-confirmation`コンポーネントが`send-message`内で使用されている場合を考えてみましょう。
子コンポーネントに渡す`sendMessage`アクションには、メッセージの種類を引数で指定できます。

```app/components/send-message.js
import Component from '@ember/component';

export default Component.extend({
  actions: {
    sendMessage(messageType) {
      //send message here and return a promise
    }
  }
});
```

<!--
However,
the `button-with-confirmation` component invoking the action doesn't know or care what type of message it's collecting.
In cases like this, the parent template can provide the required parameter when the action is passed to the child.
For example, if we want to use the button to send a message of type `"info"`:
-->

ただし、アクションを呼び出す`button-with-confirmation`コンポーネントは、メッセージの種類を知らないか気にしません。
このような場合、必要な引数をアクションと一緒に親テンプレートで子コンポーネントに渡すことができます。
例えば、`"info"`の種類のメッセージを送信する場合は、次のようになります。

```app/templates/components/send-message.hbs
{{button-with-confirmation
  text="Click to send your message."
  onConfirm=(action "sendMessage" "info")
}}
```

<!--
Within `button-with-confirmation`, the code in the `submitConfirm` action does not change.
It will still invoke `onConfirm` without explicit arguments:
-->

`button-with-confirmation`は、`submitConfirm`アクションのコードの変更はしません。
引き続き、明示的な引数なしで`onConfirm`を呼び出します。

```app/components/button-with-confirmation.js
const promise = this.get('onConfirm')();
```

<!--
However the expression `(action "sendMessage" "info")` used in passing the action to the component creates a closure,
i.e. an object that binds the parameter we've provided to the function specified.
So now when the action is invoked, that parameter will automatically be passed as its argument, effectively calling `sendMessage("info")`,
despite the argument not appearing in the calling code.
-->

なぜかというと、アクションをコンポーネントに渡す際に使用した式`(action "sendMessage" "info")`はクロージャーを作成するからです。
クロージャーは指定した関数と渡した引数がバインドされたオブジェクトです。
したがって、アクションが呼び出されると、バインドされた引数が引数として自動的に渡され、アクションを呼び出している部分で引数が書かれていないにも関わらず、実際には`sendMessage("info")`が呼び出されます。

<!--
So far in our example, the action we have passed to `button-with-confirmation` is a function that accepts one argument,
`messageType`.
Suppose we want to extend this by allowing `sendMessage` to take a second argument,
the actual text of the message the user is sending:
-->

これまでの例では、 `button-with-confirmation`に渡されたアクションは、`messageType`という引数を1つ取る関数でした。
次は`sendMessage`の第2引数に、ユーザーが送信するメッセージのテキストを取るように変更しましょう。

```app/components/send-message.js
import Component from '@ember/component';

export default Component.extend({
  actions: {
    sendMessage(messageType, messageText) {
      //send message here and return a promise
    }
  }
});
```

<!--
We want to arrange for the action to be invoked from within `button-with-confirmation` with both arguments.
We've seen already that if we provide a `messageType` value to the `action` helper when we insert `button-with-confirmation` into its parent `send-message` template,
that value will be passed to the `sendMessage` action as its first argument automatically when invoked as `onConfirm`.
If we subsequently pass a single additional argument to `onConfirm` explicitly,
that argument will be passed to `sendMessage` as its second argument
(This ability to provide arguments to a function one at a time is known as [currying](https://en.wikipedia.org/wiki/Currying)).
-->

この2つの引数で`button-with-confirmation`の中から呼び出されるアクションを手配したいと思っています。
先程、`send-message`テンプレートに`button-with-confirmation`を挿入する時に、`action`ヘルパーに`messageType`値を与えると、
`onConfirm`として呼び出された時に、その値が`sendMessage`アクションの第1引数に自動的に渡されることを確認しました。
続いて明示的に`onConfirm`に引数を一つを渡すと、
その引数は2番目の引数として`sendMessage`に渡されます。
(別のところで関数に引数を渡せるこの機能は[カリー化](https://ja.wikipedia.org/wiki/%E3%82%AB%E3%83%AA%E3%83%BC%E5%8C%96)と呼ばれます)。

<!--
In our case, the explicit argument that we pass to `onConfirm` will be the required `messageText`.
However, remember that internally our `button-with-confirmation` component does not know or care that it is being used in a messaging application.
Therefore within the component's javascript file,
we will use a property `confirmValue` to represent that argument and pass it to `onConfirm` as shown here:
-->

上記の場合、`onConfirm`に明示的に渡す引数の`messageText`は省略できません。
しかし、内部的には、`button-with-confirmation`コンポーネントはその引数がアプリケーションで使われていることは知らないか気にしません。
したがって、以下に示すように、コンポーネントのJavaScriptファイル内で、
その引数を表すために`confirmValue`というプロパティを使い、`onConfirm`に渡します。

```app/components/button-with-confirmation.js
import Component from '@ember/component';

export default Component.extend({
  actions: {
    //...
    submitConfirm() {
      // call onConfirm with a second argument
      let promise = this.get('onConfirm')(this.get('confirmValue'));
      promise.then(() => {
        this.set('confirmShown', false);
      });
    },
    //...
  }
});
```

<!--
In order for `confirmValue` to take on the value of the message text,
we'll bind the property to the value of a user input field that will appear when the button is clicked.
To accomplish this,
we'll first modify the component so that it can be used in block form and we will [yield](../wrapping-content-in-a-component/) `confirmValue` to the block within the `"confirmDialog"` element:
-->

`confirmValue`がメッセージテキストの値を得るためには、ボタンをクリックしたときに表示されるテキストフィールドの値とこのプロパティをバインドします。
これを実現するために、、まずブロック形式で使用できるようにコンポーネントを変更し、`confirmDialog`要素内のブロックに`confirmValue`を[yield](../wrapping-content-in-a-component/)で渡します。


```app/templates/components/button-with-confirmation.hbs
<button {{action "launchConfirmDialog"}}>{{text}}</button>
{{#if confirmShown}}
  <div class="confirm-dialog">
    {{yield confirmValue}}
    <button class="confirm-submit" {{action "submitConfirm"}}>OK</button>
    <button class="confirm-cancel" {{action "cancelConfirm"}}>Cancel</button>
  </div>
{{/if}}
```

<!--
With this modification,
we can now use the component in `send-message` to wrap a text input element whose `value` attribute is set to `confirmValue`:
-->

この変更により、`send-message`の中でこのコンポーネントを使用してテキストフィールドをラップし、テキストフィールドの`value`属性を`confirmValue`に設定することができます。

```app/templates/components/send-message.hbs
{{#button-with-confirmation
    text="Click to send your message."
    onConfirm=(action "sendMessage" "info")
    as |confirmValue|}}
  {{input value=confirmValue}}
{{/button-with-confirmation}}
```

<!--
When the user enters their message into the input field,
the message text will now be available to the component as `confirmValue`.
Then, once they click the "OK" button, the `submitConfirm` action will be triggered, calling `onConfirm` with the provided `confirmValue`,
thus invoking the `sendMessage` action in `send-message` with both the `messageType` and `messageText` arguments.
-->

ユーザーがメッセージをテキストフィールドに入力した値は、コンポーネントの`confirmValue`として参照できます。
次に、"OK"ボタンをクリックすると、`submitConfirm`アクションが実行され、`onConfirm`に`confirmValue`を渡して呼び出し、
`send-message`コンポーネントの`sendMessage`アクションが`messageType`と`messageText`が引数に渡されて呼び出されます。

<!--
## Invoking Actions Directly on Component Collaborators
-->

## コンポーネント内のオブジェクトのアクションを直接呼び出す

<!--
Actions can be invoked on objects other than the component directly from the template.  For example, in our
`send-message` component we might include a service that processes the `sendMessage` logic.
-->

アクションは、テンプレートからコンポーネント以外のオブジェクトに対しても直接呼び出すことができます。
例えば、`send-message`コンポーネントで、`sendMessage`ロジックを処理するサービスを入れたとします。

```app/components/send-message.js
import Component from '@ember/component';
import { inject as service } from '@ember/service';

export default Component.extend({
  messaging: service(),

  // component implementation
});
```

<!--
We can tell the action to invoke the `sendMessage` action directly on the messaging service with the `target` attribute.
-->

`target`属性を使ってmessagingサービスの`sendMessage`を直接呼び出すようにアクションを伝えることができます。

```app/templates/components/send-message.hbs
{{#button-with-confirmation
    text="Click to send your message."
    onConfirm=(action "sendMessage" "info" target=messaging)
    as |confirmValue| }}
  {{input value=confirmValue}}
{{/button-with-confirmation}}
```

<!--
By supplying the `target` attribute, the action helper will look to invoke the `sendMessage` action directly on the messaging
service, saving us from writing code on the component that just passes the action along to the service.
-->

`target`属性を指定することで、actionヘルパーはmessagingサービスの`sendMessage`を直接呼び出すようになり、アクションをサービスに渡すだけのコードをコンポーネントに書かなくて済みます。

```app/services/messaging.js
import Service from '@ember/service';

export default Ember.Service.extend({
  actions: {
    sendMessage(messageType, text) {
      //handle message send and return a promise
    }
  }
});
```

<!--
## Destructuring Objects Passed as Action Arguments
-->

## アクションの引数に渡したオブジェクトの分解

<!--
A component will often not know what information a parent needs to process an action, and will just pass all the
information it has.
For example, our `user-profile` component is going to notify its parent, `system-preferences-editor`, that a
user's account was deleted, and passes along with it the full user profile object.
-->

多くの場合、子コンポーネントは、親がアクションを処理するために必要な情報を把握せず、ただ持っている情報をすべて渡すことがあります。
例えば、`user-profile`コンポーネントは、親の`system-preferences-editor`に、ユーザーのアカウントが削除されたことを通知し、ユーザープロファイルオブジェクトごと渡します。

```app/components/user-profile.js
import Component from '@ember/component';
import { inject as service } from '@ember/service';

export default Component.extend({
  login: service(),

  actions: {
    userDidDeleteAccount() {
      this.get('login').deleteUser();
      this.get('didDelete')(this.get('login.currentUserObj'));
    }
  }
});
```

<!--
All our `system-preferences-editor` component really needs to process a user deletion is an account ID.
For this case, the action helper provides the `value` attribute to allow a parent component to dig into the passed
object to pull out only what it needs.
-->

`system-preferences-editor`コンポーネントがユーザーの削除に必要な情報はそのユーザーIDだけです。
そのような場合のために、アクションヘルパーには、親コンポーネントが渡されたオブジェクから必要なものだけを取得できる`value`属性があります。

```app/templates/components/system-preferences-editor.hbs
{{user-profile didDelete=(action "userDeleted" value="account.id")}}
```

<!--
Now when the `system-preferences-editor` handles the delete action, it receives only the user's account `id` string.
-->

これにより、`system-preferences-editor`が削除アクションを実行する時はユーザーのアカウント`id`だけを受け取るようになります。。

```app/components/system-preferences-editor.js
import Component from '@ember/component';

export default Component.extend({
  actions: {
    userDeleted(idStr) {
      //respond to deletion
    }
  }
});
```

<!--
## Calling Actions Up Multiple Component Layers
-->

## 複数のコンポーネントレイヤー上のアクションの実行

<!--
When your components go multiple template layers deep, it is common to need to handle an action several layers up the tree. 
Using the action helper, parent components can pass actions to child components through templates alone without adding JavaScript code to those child components.
-->

コンポーネントが複数のテンプレートレイヤーに深くなると、いくつか上のレイヤーで処理する必要がある時がよくあります。
actionヘルパーを使う時に、テンプレートだけでアクションを親コンポーネントから子コンポーネントに渡すことができます。
JavaScriptコードを子コンポーネントに追加する必要はありません。

<!--
For example, say we want to move account deletion from the `user-profile` component to its parent `system-preferences-editor`.
-->

例えば、アカウントの削除を`user-profile`コンポーネントから親`system-preferences-editor`に移したいとしましょう。

<!--
First we would move the `deleteUser` action from `user-profile.js` to the actions object on `system-preferences-editor`.
-->

まず`deleteUser`アクションを`user-profile.js`から `system-preferences-editor`のactionsオブジェクトに移します。

```app/components/system-preferences-editor.js
import Component from '@ember/component';
import { inject as service } from '@ember/service';

export default Component.extend({
  login: service(),
  actions: {
    deleteUser(idStr) {
      return this.get('login').deleteUserAccount(idStr);
    }
  }
});
```

<!--
Then our `system-preferences-editor` template passes its local `deleteUser` action into the `user-profile` as that
component's `deleteCurrentUser` property.
-->

```app/templates/components/system-preferences-editor.hbs
{{user-profile
  deleteCurrentUser=(action 'deleteUser' login.currentUser.id)
}}
```

次に、`system-preferences-editor`テンプレートでローカルの`deleteUser`アクションを`user-profile`コンポーネントの`deleteCurrentUser`プロパティとして渡します。

<!--
The action `deleteUser` is in quotes, since `system-preferences-editor` is where the action is defined now. Quotes indicate that the action should be looked for in `actions` local to that component, rather than in those that have been passed from a parent.
-->

アクション`deleteUser`は引用符で囲まれています。
これは、`system-preferences-editor`にアクションが定義されているためです。
引用符は、アクションが、親から渡されたアクションではなく、そのコンポーネントにローカルな`actions`で検索されるべきであることを示します。

<!--
In our `user-profile.hbs` template we change our action to call `deleteCurrentUser` as passed above.
-->

`user-profile.hbs`テンプレートでは、上記で指定した`deleteCurrentUser`を呼び出すようにアクションを変更します。

```app/templates/components/user-profile.hbs
{{button-with-confirmation
  onConfirm=(action deleteCurrentUser)
  text="Click OK to delete your account."
}}
```

<!--
Note that `deleteCurrentUser` is no longer in quotes here as opposed to [previously](#toc_passing-the-action-to-the-component). Quotes are used to initially pass the action down the component tree, but at every subsequent level you are instead passing the actual function reference (without quotes) in the action helper.
-->

`deleteCurrentUser`は、[前述](#toc_アクションをコンポーネントに渡す)とは違い、ここで引用符で囲まれていないことに注意してください。
引用符は、最初にアクションをコンポーネントツリーに渡すために使用されますが、後続の各レベルでは、実際の関数リファレンス(引用符なし)をactionヘルパーに渡しています。

<!--
Now when you confirm deletion, the action goes straight to the `system-preferences-editor` to be handled in its local context.
-->

変更後、削除を許可すると、アクションが`system-preferences-editor`に直接送られ、ローカルコンテキストで処理されます。
