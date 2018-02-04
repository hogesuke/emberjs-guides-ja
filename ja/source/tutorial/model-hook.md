<!--
Now, let's add a list of available rentals to the rentals page we've just created.
-->

先程作成した物件一覧ページに利用可能な物件を表示しましょう。

<!--
Ember keeps data for a page in an object called a `model`.
To keep things simple at first,
we'll populate the model for our rental listing page to use a hard-coded array of JavaScript objects.
Later, we'll switch to using [Ember Data](https://github.com/emberjs/data),
a library for robustly managing data in our app.
-->

Emberは、`model`と呼ばれるオブジェクトのページのデータを保持します。
最初は物事を単純にするため、物件一覧ページのモデルにJavaScriptオブジェクトの配列をハードコードします。
その次に、アプリでデータを堅牢に管理するためのライブラリである[Ember Data](https://github.com/emberjs/data)の使用に切り替えます。

<!--
Here's what our homepage will look like when we're done:
-->

次のようになります。

![super rentals homepage with rentals list](../../images/model-hook/super-rentals-index-with-list.png)

<!--
In Ember, route handlers are responsible for loading the model with data for the page.
It loads the data in a function called [`model`](https://www.emberjs.com/api/ember/2.16/classes/Route/methods/model?anchor=model).
The `model` function acts as a **hook**, meaning that Ember will call it for us during different times in our app.
The model function we've added to our `rentals` route handler will be called when a user navigates to the rentals route via root URL `http://localhost:4200`, or via `http://localhost:4200/rentals`.
-->

Emberでは、ルートハンドラがページのデータをモデルにロードします。
[`model`](https://www.emberjs.com/api/ember/2.16/classes/Route/methods/model?anchor=model)と呼ばれる関数で行います。
`model`関数は、その時々において実行される**フック**です。
`rentals`ルートハンドラに追加したモデル関数は、ルートURL`http://localhost:4200`または`http:// localhost:4200/rentals`経由でレンタルルートにナビゲートすると呼び出されます。

<!--
Let's open `app/routes/rentals.js` and return an array of rental objects from the `model` function:
-->

`app/routes/rentals.js`を開き、`model`関数から物件オブジェクトの配列を返しましょう：

```app/routes/rentals.js{+4,+5,+6,+7,+8,+9,+10,+11,+12,+13,+14,+15,+16,+17,+18,+19,+20,+21,+22,+23,+24,+25,+26,+27,+28,+29,+30,+31,+32,+33}
import Route from '@ember/routing/route';

export default Route.extend({
  model() {
    return [{
      id: 'grand-old-mansion',
      title: 'Grand Old Mansion',
      owner: 'Veruca Salt',
      city: 'San Francisco',
      category: 'Estate',
      bedrooms: 15,
      image: 'https://upload.wikimedia.org/wikipedia/commons/c/cb/Crane_estate_(5).jpg',
      description: 'This grand old mansion sits on over 100 acres of rolling hills and dense redwood forests.'
    }, {
      id: 'urban-living',
      title: 'Urban Living',
      owner: 'Mike TV',
      city: 'Seattle',
      category: 'Condo',
      bedrooms: 1,
      image: 'https://upload.wikimedia.org/wikipedia/commons/0/0e/Alfonso_13_Highrise_Tegucigalpa.jpg',
      description: 'A commuters dream. This rental is within walking distance of 2 bus stops and the Metro.'
    }, {
      id: 'downtown-charm',
      title: 'Downtown Charm',
      owner: 'Violet Beauregarde',
      city: 'Portland',
      category: 'Apartment',
      bedrooms: 3,
      image: 'https://upload.wikimedia.org/wikipedia/commons/f/f7/Wheeldon_Apartment_Building_-_Portland_Oregon.jpg',
      description: 'Convenience is at your doorstep with this charming downtown rental. Great restaurants and active night life are within a few feet.'
    }];
  }
});
```

<!--
Note that here, we are using the ES6 shorthand method definition syntax: `model()` is the same as writing `model: function()`.
-->

ここでは、ES6の短縮形のメソッド定義構文を使用していることに注意してください。 `model()`は `model: function()`と同じです。

<!--
Ember will use the model object returned above and save it as an attribute called `model`,
available to the rentals template we generated with our route in [Routes and Templates](../routes-and-templates/#toc_a-rentals-route).
-->

Emberは上記で返されたモデルオブジェクトを使用し、 `model`という名前の属性として保存します。これは、[ルートとテンプレート](../routes-and-templates/#toc_a-rentals-route)でルートと一緒に生成したrentalsテンプレートで利用できます。

<!--
Now, let's switch over to our rentals page template.
We can use the model attribute to display our list of rentals.
Here, we'll use another common Handlebars helper called [`{{each}}`](../../templates/displaying-a-list-of-items/).
This helper will let us loop through each of the rental objects in our model:
-->

次に、物件一覧のページテンプレートを開きましょう。
model属性を使用してレンタルリストを表示することができます。
ここでは、[`{{each}}`](../../templates/displays-a-list-of-items/)という別のHandlebarsヘルパーを使用します。
このヘルパーは、モデル内の各物件オブジェクトをループさせます。

```app/templates/rentals.hbs{+12,+13,+14,+15,+16,+17,+18,+19,+20,+21,+22,+23,+24,+25,+26,+27,+28,+29}
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
In this template, we loop through each object.
On each iteration, the current object gets stored in a variable called `rental`.
From the rental variable in each step, we create a listing with information about the property.
-->

上記のコードは、テンプレート内で各オブジェクトをループしています。
ループ毎に渡されるオブジェクトを`rental`という変数に格納しています。
rentals変数から物件に関する情報を列挙しています。

<!--
You may move onto the [next page](../installing-addons/) to keep implementing new features, or continue reading on testing the app you've created.
-->

新しい機能の実装を続けるには[次のページ](../installing-addons/)に行ってください。
ここから先は、ここまでで作成した昨日のテストの実装です。

<!--
### Acceptance Testing the Rental List
-->

### 物件一覧の受け入れテスト

<!--
To check that rentals are listed with an automated test, we will create a test to visit the index route and check that the results show 3 listings.
-->

物件一覧が表示されていることを自動テストで確認するために、インデックスルートにアクセスして、3件表示されるかどうかを確認するテストを作成します。

<!--
In `app/templates/rentals.hbs`, we wrapped each rental display in an `article` element, and gave it a class called `listing`.
We will use the listing class to find out how many rentals are shown on the page.
-->
`app/templates/rentals.hbs`では、各物件情報を`listing`というクラスを持った`article`要素でラップました。
listingクラスを使用して、ページに表示される物件数を確認します。

<!--
To find the elements that have a class called `listing`, we'll use a test helper called [find](http://emberjs.com/api/classes/Ember.Test.html#method_find).
The `find` function returns the elements that match the given [CSS selector](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors).
In this case it will return an array of all the elements with a class called `listing`.
-->

`listing`というクラスを持つ要素を見つけるために、[find](http://emberjs.com/api/classes/Ember.Test.html#method_find)というテストヘルパーを使います。
`find`関数は与えられた[CSS selectors(https://developer.mozilla.org/ja-JP/docs/Web/CSS/CSS_Selectors)と一致する要素を返します。
この場合、 `listing`というクラスを持つすべての要素の配列を返します。


```/tests/acceptance/list-rentals-test.js{+2,+3,+4,+5}
test('should list available rentals.', function (assert) {
  visit('/');
  andThen(function() {
    assert.equal(find('.listing').length, 3, 'should see 3 listings');
  });
});
```

<!--
Run the tests again using the command `ember t -s`, and toggle "Hide passed tests" to show your new passing test.
-->

`ember t -s`でテストを実行し、"Hide passed tests"を切り替えて、追加したテストがパスしたか確認してください。

<!--
Now we are listing rentals, and verifying it with an acceptance test.
This leaves us with 2 remaining acceptance test failures (and 1 eslint failure):
-->

物件一覧を表示していることをテストで検証することができました。
これにより、残りの失敗しているテストが２件になりました。（ESLintを加えると３件）

![list rentals test passing](../../images/model-hook/model-hook.png)
