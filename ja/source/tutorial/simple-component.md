<!--
As a user looks through our list of rentals, they may want to have some interactive options to help them make a decision.
Let's add the ability to toggle the size of the image for each rental.
To do this, we'll use a component.
-->

ユーザーが物件を調べる際には、どの物件にするか決めるのに役立つインタラクティブなオプションが必要な場合があります。
物件ごとに画像のサイズを切り替える機能を追加しましょう。
これを行うには、コンポーネントを使用します。

<!--
Let's generate a `rental-listing` component that will manage the behavior for each of our rentals.
A dash is required in every component name to avoid conflicting with a possible HTML element,
so `rental-listing` is acceptable but `rental` isn't.
-->

各物件の動作を管理する `rental-listing`コンポーネントを生成しましょう。
HTML要素との競合を避けるために、すべてのコンポーネント名にダッシュが必要です。
`rental-listing`は使用可能ですが`rental`は使用できません。

```shell
ember g component rental-listing
```

<!--
Ember CLI will then generate a handful of files for our component:
-->

ファイルが作成されます。

```shell
installing component
  create app/components/rental-listing.js
  create app/templates/components/rental-listing.hbs
installing component-test
  create tests/integration/components/rental-listing-test.js
```

<!--
A component consists of two parts:
-->

コンポーネントは2つの部品で構成されています。

<!--
* A template that defines how it will look (`app/templates/components/rental-listing.hbs`)
* A JavaScript source file (`app/components/rental-listing.js`) that defines how it will behave.
-->

* どのように見えるかを定義するテンプレート (`app/templates/components/rental-listing.hbs`)
* どのように動作するかを定義するJavaScriptソースファイル(`app/components/rental-listing.js`)

<!--
Our new `rental-listing` component will manage how a user sees and interacts with a rental.
To start, let's move the rental display details for a single rental from the `rentals.hbs` template into `rental-listing.hbs` and add the image field:
-->

`rental-listing`コンポーネントは、ユーザーが物件を見たり操作する方法を管理します。
まず、１物件の詳細情報を`rentals.hbs`テンプレートから`rental-listing.hbs`に移し、画像フィールドを追加しましょう。

```app/templates/components/rental-listing.hbs{-1,+2,+3,+4,+5,+6,+7,+8,+9,+10,+11,+12,+13,+14,+15,+16,+17}
{{yield}}
<article class="listing">
  <img src="{{rental.image}}" alt="">
  <h3>{{rental.title}}</h3>
  <div class="detail owner">
    <span>Owner:</span> {{rental.owner}}
  </div>
  <div class="detail type">
    <span>Type:</span> {{rental.category}}
  </div>
  <div class="detail location">
    <span>Location:</span> {{rental.city}}
  </div>
  <div class="detail bedrooms">
    <span>Number of bedrooms:</span> {{rental.bedrooms}}
  </div>
</article>
```

<!--
Now in our `rentals.hbs` template, let's replace the old HTML markup within our `{{#each}}` loop
with our new `rental-listing` component:
-->

次に、`rentals.hbs`テンプレートの`{{#each}}`ループ内の古いHTMLマークアップを置き換えましょう。
`rental-listing`コンポーネントを使います。


```app/templates/rentals.hbs{+12,+13,-14,-15,-16,-17,-18,-19,-20,-21,-22,-23,-24,-25,-26,-27,-28,-29}
<div class="jumbo">
  <div class="right tomster"></div>
  <h2>Welcome!</h2>
  <p>
    We hope you find exactly what you're looking for in a place to stay.
  </p>
  {{#link-to "about" class="button"}}
    About Us
  {{/link-to}}
</div>

{{#each model as |rentalUnit|}}
  {{rental-listing rental=rentalUnit}}
{{#each model as |rental|}}
  <article class="listing">
    <h3>{{rental.title}}</h3>
    <div class="detail owner">
      <span>Owner:</span> {{rental.owner}}
    </div>
    <div class="detail type">
      <span>Type:</span> {{rental.category}}
    </div>
    <div class="detail location">
      <span>Location:</span> {{rental.city}}
    </div>
    <div class="detail bedrooms">
      <span>Number of bedrooms:</span> {{rental.bedrooms}}
    </div>
  </article>
{{/each}}
```

<!--
Here we invoke the `rental-listing` component by name, and assign each `rentalUnit` as the `rental` attribute of the component.
-->

ここでは、`rental-listing`コンポーネントを名前で呼び出し、各`rentalUnit`をコンポーネントの`rental`属性として割り当てます。

<!--
Our app should behave now as before, with the addition of an image for each rental item.
-->

アプリは以前と同じように動作し、各レンタルアイテムの画像が追加されたのがわかります。

![App with component and images](../../images/simple-component/app-with-images.png)

<!--
## Hiding and Showing an Image
-->

## 画像の非表示と表示

<!--
Now we can add functionality that will show the image of a rental when requested by the user.
-->

ユーザーがリクエストしたときに物件画像を表示する機能を追加します。

<!--
Let's use the `{{if}}` helper to show our current rental image larger only when `isWide` is set to true, by setting the element class name to `wide`.
We'll also add some text to indicate that the image can be clicked on,
and wrap both with an anchor element,
giving it the `image` class name so that our test can find it.
-->

`{{if}}`ヘルパーを使って、`isWide`がtrueの場合にのみ要素のクラス名を`wide`に設定し、物件画像を大きくします。
また、画像をクリックすることができることを示すテキストを追加し、A要素で両方をラップします。
A要素にはテストで見つけることができるようにクラス属性に`image`を設定します。

```app/templates/components/rental-listing.hbs{+2,+4,+5}
<article class="listing">
  <a class="image {{if isWide "wide"}}">
    <img src="{{rental.image}}" alt="">
    <small>View Larger</small>
  </a>
  <h3>{{rental.title}}</h3>
  <div class="detail owner">
    <span>Owner:</span> {{rental.owner}}
  </div>
  <div class="detail type">
    <span>Type:</span> {{rental.category}}
  </div>
  <div class="detail location">
    <span>Location:</span> {{rental.city}}
  </div>
  <div class="detail bedrooms">
    <span>Number of bedrooms:</span> {{rental.bedrooms}}
  </div>
</article>
```

<!--
The value of `isWide` comes from our component's JavaScript file, in this case `rental-listing.js`.
Since we want the image to be smaller at first, we will set the property to start as `false`:
-->

`isWide`の値は、コンポーネントのJavaScriptファイル(この場合は` rental-listing.js`)から来ています。
最初は画像を小さくしたいので、プロパティを`false`で始めるように設定します:


```app/components/rental-listing.js{+4}
import Component from '@ember/component';

export default Component.extend({
  isWide: false
});
```

<!--
To allow the user to widen the image, we will need to add an action that toggles the value of `isWide`.
Let's call this action `toggleImageSize`
-->

ユーザーが画像を拡大できるようにするには、`isWide`の値をトグルするアクションを追加する必要があります。
このアクションを`toggleImageSize`と呼ぶことにしましょう。

```app/templates/components/rental-listing.hbs{-2,+3}
<article class="listing">
  <a class="image {{if isWide "wide"}}">
  <a {{action 'toggleImageSize'}} class="image {{if isWide "wide"}}">
    <img src="{{rental.image}}" alt="">
    <small>View Larger</small>
  </a>
  <h3>{{rental.title}}</h3>
  <div class="detail owner">
    <span>Owner:</span> {{rental.owner}}
  </div>
  <div class="detail type">
    <span>Type:</span> {{rental.category}}
  </div>
  <div class="detail location">
    <span>Location:</span> {{rental.city}}
  </div>
  <div class="detail bedrooms">
    <span>Number of bedrooms:</span> {{rental.bedrooms}}
  </div>
</article>
```
<!--
Clicking the anchor element will send the action to the component.
Ember will then go into the `actions` hash and call the `toggleImageSize` function.
-->

A要素をクリックすると、コンポーネントにアクションが送信されます。
Emberは `actions`ハッシュ内の`toggleImageSize`関数を呼び出します。

<!--
An [actions hash](../../templates/actions/) is an object in the component that contains functions.
These functions are called when the user interacts with the UI, such as clicking.
-->

[actionsハッシュ](../../templates/actions/)は、関数を含むコンポーネント内のオブジェクトです。
これらの関数は、ユーザーがクリックするなど、UIと対話するときに呼び出されます。

<!--
Let's create the `toggleImageSize` function and toggle the `isWide` property on our component:
-->

`toggleImageSize`関数を作成し、コンポーネントのisWideプロパティを切り替えましょう:

```app/components/rental-listing.js{-4,+5,+6,+7,+8,+9,+10}
import Component from '@ember/component';

export default Component.extend({
  isWide: false
  isWide: false,
  actions: {
    toggleImageSize() {
      this.toggleProperty('isWide');
    }
  }
});
```

<!--
Now when we click the image or the `View Larger` link in our browser, we  see our image show larger.
When we click the enlarged image again, we see it smaller.
-->

画像をクリックするか、`View Larger`リンクをクリックすると、画像が大きく表示されます。
拡大した画像を再度クリックすると、小さく表示されます。


![rental listing with expand](../../images/simple-component/styled-rental-listings.png)

<!--
Move on to the [next page](../hbs-helper/) for the next feature, or continue on here to test what you just wrote.
-->

次のページに[行くか](../hbs-helper/)、テストを書きましょう。

<!--
### An Integration Test
-->

### 統合テスト

<!--
Ember components are commonly tested with [component integration tests](../../testing/testing-components/).
Component integration tests verify the behavior of a component within the context of Ember's rendering engine.
When running in an integration test, the component goes through its regular [render lifecycle](../../components/the-component-lifecycle/),
and has access to dependent objects, loaded through Ember's resolver.
-->

Emberコンポーネントは一般的に[コンポーネント統合テスト](../../testing/testing-components/)でテストします。
コンポーネント統合テストは、Emberのレンダリングエンジンのコンテキスト内でコンポーネントの動作を検証します。
統合テストで実行する場合、コンポーネントは通常の[レンダリングライフサイクル](../../components/the-component-lifecycle/)を経て、Emberのリゾルバによってロードされた依存オブジェクトにアクセスできます。

<!--
Our component integration test will test two different behaviors:
-->

コンポーネント統合テストでは、2つの異なる動作をテストします。

<!--
* The component should show details about the rental
* The component should toggle the existence of a wide class on click, to expand and shrink the photo of the rental.
-->

* 物件に関する詳細情報が表示されること
* クリック時にwideクラスの存在を切り替え、レンタルの写真を拡大縮小すること

<!--
Let's update the default test to contain the scenarios we want to verify:
-->

確認したいシナリオが含まれるようにデフォルトのテストを更新しましょう:

```tests/integration/components/rental-listing-test.js{+3,+4,+9,+10,+11,+12,+13,+14,+15,+16,-17,-18,-19,-20,-21,-22,-23,-24,-25,-26,-27,-28,-29,-30,-31,-32,-33,-34}
import { moduleForComponent, test } from 'ember-qunit';
import hbs from 'htmlbars-inline-precompile';
import EmberObject from '@ember/object';
import { run } from '@ember/runloop';

moduleForComponent('rental-listing', 'Integration | Component | rental listing', {
  integration: true
});

test('should display rental details', function(assert) {

});

test('should toggle wide class on click', function(assert) {

});
test('it renders', function(assert) {

  // Set any properties with this.set('myProperty', 'value');
  // Handle any actions with this.on('myAction', function(val) { ... });

  this.render(hbs`{{rental-listing}}`);

  assert.equal(this.$().text().trim(), '');

  // Template block usage:
  this.render(hbs`
    {{#rental-listing}}
      template block text
    {{/rental-listing}}
  `);

  assert.equal(this.$().text().trim(), 'template block text');
});
```

<!--
For the test we'll pass the component a fake object that has all the properties that our rental model has.
We'll give the variable the name `rental`, and in each test we'll set `rental` to our local scope, represented by the `this` object.
The render template can access values in local scope.
-->

テストのために、rentalモデルが持つすべてのプロパティを持つ擬似オブジェクトをコンポーネントに渡します。
変数に`rental`という名前を付け、各テストで`this`オブジェクトで表されるローカルスコープに`rental`を設定します。
レンダーテンプレートはローカルスコープの値にアクセスできます。


```tests/integration/components/rental-listing-test.js{+6,+7,+8,+9,+10,+11,+12,+13,+20,+24}
import { moduleForComponent, test } from 'ember-qunit';
import hbs from 'htmlbars-inline-precompile';
import EmberObject from '@ember/object';
import { run } from '@ember/runloop';

let rental = EmberObject.create({
  image: 'fake.png',
  title: 'test-title',
  owner: 'test-owner',
  category: 'test-type',
  city: 'test-city',
  bedrooms: 3
});

moduleForComponent('rental-listing', 'Integration | Component | rental listing', {
  integration: true
});

test('should display rental details', function(assert) {
  this.set('rentalObj', rental);
});

test('should toggle wide class on click', function(assert) {
  this.set('rentalObj', rental);
});
```

<!--
Now let's render our component using the `render` function.
The `render` function allows us to pass a template string, so that we can declare the component in the same way we do in our templates.
Since we set the `rentalObj` variable to our local scope, we can access it as part of our render string.
-->

次に、 `render`関数を使ってコンポーネントをレンダリングしましょう。
`render`関数はテンプレート文字列を渡すことができるので、テンプレートと同じ方法でコンポーネントを宣言することができます。
`rentalObj`変数をローカルスコープに設定したので、レンダリング文字列の一部としてレンダリングすることができます。

```tests/integration/components/rental-listing-test.js{+21,+26}
import { moduleForComponent, test } from 'ember-qunit';
import hbs from 'htmlbars-inline-precompile';
import EmberObject from '@ember/object';
import { run } from '@ember/runloop';

let rental = EmberObject.create({
  image: 'fake.png',
  title: 'test-title',
  owner: 'test-owner',
  category: 'test-type',
  city: 'test-city',
  bedrooms: 3
});

moduleForComponent('rental-listing', 'Integration | Component | rental listing', {
  integration: true
});

test('should display rental details', function(assert) {
  this.set('rentalObj', rental);
  this.render(hbs`{{rental-listing rental=rentalObj}}`);
});

test('should toggle wide class on click', function(assert) {
  this.set('rentalObj', rental);
  this.render(hbs`{{rental-listing rental=rentalObj}}`);
});
```

<!--
Finally, let's add our actions and assertions.
-->

最後に、アクションとアサーションを追加しましょう。

<!--
In the first test, we just want to verify the output of the component, so we just assert that the title and owner text match what we provided in the fake `rental`.
-->

最初のテストでは、コンポーネントの出力を確認したいだけなので、タイトルと所有者のテキストが`rental`で提供したものと一致するか検証します。

```tests/integration/components/rental-listing-test.js{+4,+5}
test('should display rental details', function(assert) {
  this.set('rentalObj', rental);
  this.render(hbs`{{rental-listing rental=rentalObj}}`);
  assert.equal(this.$('.listing h3').text(), 'test-title', 'Title: test-title');
  assert.equal(this.$('.listing .owner').text().trim(), 'Owner: test-owner', 'Owner: test-owner');
});
```

<!--
In the second test, we verify that clicking on the image toggles the size.
We will assert that the component is initially rendered without the `wide` class name.
Clicking the image will add the class `wide` to our element, and clicking it a second time will take the `wide` class away.
Note that we find the image element using the CSS selector `.image`.
-->

2番目のテストでは、画像をクリックするとサイズが切り替わることを確認します。
コンポーネントが`wide`クラス名なしでレンダリングされることを確認します。
画像をクリックすると、クラスに`wide`クラスが追加され、2回目にクリックすると`wide`クラスが削除されます。
image要素はCSSセレクタ`.image`を使って検索していることに注意してください。


```tests/integration/components/rental-listing-test.js{+4,+5,+6,+7,+8}
test('should toggle wide class on click', function(assert) {
  this.set('rentalObj', rental);
  this.render(hbs`{{rental-listing rental=rentalObj}}`);
  assert.equal(this.$('.image.wide').length, 0, 'initially rendered small');
  run(() => document.querySelector('.image').click());
  assert.equal(this.$('.image.wide').length, 1, 'rendered wide after click');
  run(() => document.querySelector('.image').click());
  assert.equal(this.$('.image.wide').length, 0, 'rendered small after second click');
});
```

<!--
The final test should look as follows:
-->

テストは最終的に次のようになります。

```tests/integration/components/rental-listing-test.js
import { moduleForComponent, test } from 'ember-qunit';
import hbs from 'htmlbars-inline-precompile';
import EmberObject from '@ember/object';
import { run } from '@ember/runloop';

let rental = EmberObject.create({
  image: 'fake.png',
  title: 'test-title',
  owner: 'test-owner',
  category: 'test-type',
  city: 'test-city',
  bedrooms: 3
});

moduleForComponent('rental-listing', 'Integration | Component | rental listing', {
  integration: true
});

test('should display rental details', function(assert) {
  this.set('rentalObj', rental);
  this.render(hbs`{{rental-listing rental=rentalObj}}`);
  assert.equal(this.$('.listing h3').text(), 'test-title', 'Title: test-title');
  assert.equal(this.$('.listing .owner').text().trim(), 'Owner: test-owner', 'Owner: test-owner')
});

test('should toggle wide class on click', function(assert) {
  this.set('rentalObj', rental);
  this.render(hbs`{{rental-listing rental=rentalObj}}`);
  assert.equal(this.$('.image.wide').length, 0, 'initially rendered small');
  run(() => document.querySelector('.image').click());
  assert.equal(this.$('.image.wide').length, 1, 'rendered wide after click');
  run(() => document.querySelector('.image').click());
  assert.equal(this.$('.image.wide').length, 0, 'rendered small after second click');
});
```

<!--
Run `ember t -s` to verify that our new test is passing.
To find the new test, locate "Integration | Component | rental listing" in the "Module" field of the test UI.
-->

`ember t -s`を実行して、新しいテストがパスしていることを確認してください。
新しいテストを探すには、テストUIの"Module"フィールドで"Integration | Component | rental listing"と入力してください。


![simple_component_test](../../images/simple-component/simple-component-test.gif)
