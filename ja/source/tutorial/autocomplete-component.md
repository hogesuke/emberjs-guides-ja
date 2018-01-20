<!--
As they search for a rental, users might also want to narrow their search to a specific city.
While our [initial](../simple-component/) rental listing component only displayed rental information, this new filter component will also allow the user to provide input in the form of filter criteria.
-->

ユーザが物件を検索する際に、検索対象を特定の都市に絞りたくなるかもしれません。 [初めに作成した物件コンポーネント](../simple-component/)では物件情報の表示のみでしたが、これから作成するfilterコンポーネントでは、検索対象を入力できるようにします。

<!--
To begin, let's generate our new component.
We'll call this component `list-filter`, since all we want our component to do is filter the list of rentals based on input.
-->

それでは、コンポーネントを生成しましょう。 入力に基づいて物件一覧をフィルタするので、このコンポーネントを`list-filter`と命名します。

```shell
ember g component list-filter
```

<!--
As before when we created the [`rental-listing` component](../simple-component), the "generate component" CLI command creates
-->

[`rental-listing`](../simple-component)コンポーネントを作成する前と同様、以下のものが生成されます。

<!--
* a Handlebars template (`app/templates/components/list-filter.hbs`),
* a JavaScript file (`app/components/list-filter.js`),
* and a component integration test (`tests/integration/components/list-filter-test.js`).
-->

* Handlebarsテンプレート (`app/templates/components/list-filter.hbs`),
* JavaScriptファイル (`app/components/list-filter.js`),
* コンポーネントの統合テストファイル (`tests/integration/components/list-filter-test.js`).


<!--
#### Providing Markup to a Component
-->


#### コンポーネントのマークアップ

<!--
In our `app/templates/rentals.hbs` template file, we'll add a reference to our new `list-filter` component.
-->

`app/templates/rentals.hbs` テンプレートファイルに、作成した`list-filter`コンポーネントの参照を追加します。

<!--
Notice that below we "wrap" our rentals markup inside the open and closing mentions of `list-filter` on lines 12 and 20.
This is an example of the [**block form**](../../components/wrapping-content-in-a-component) of a component,
which allows a Handlebars template to be rendered _inside_ the component's template wherever the `{{yield}}` expression appears.
-->

以下のコードでは、12行目と20行目の`list-filter`の開きと閉じの中に物件を"ラップ"していることに注意してください。
これはコンポーネントの[**ブロック形式**](../../components/wrapping-content-in-a-component)の例であり、Handlebarsテンプレートをコンポーネントのテンプレート内の`{{yield}}`式があるところに描画します。

<!--
In this case we are passing, or "yielding", our filter data to the inner markup as a variable called `filteredResults` (line 14).
-->

この場合、フィルタリングしたデータをブロック内のマークアップに`filteredResults`という変数として渡しています（14行目）。

```app/templates/rentals.hbs{+12,+13,+14,+15,+16,+17,+18,+19,+20,-21,-22,-23}
<div class="jumbo">
  <div class="right tomster"></div>
  <h2>Welcome!</h2>
  <p>
    We hope you find exactly what you're looking for in a place to stay.
  </p>
  {{#link-to 'about' class="button"}}
    About Us
  {{/link-to}}
</div>

{{#list-filter
   filter=(action 'filterByCity')
   as |filteredResults|}}}}
  <ul class="results">
    {{#each filteredResults as |rentalUnit|}}
      <li>{{rental-listing rental=rentalUnit}}</li>
    {{/each}}
  </ul>
{{/list-filter}}
{{#each model as |rentalUnit|}}
  {{rental-listing rental=rentalUnit}}
{{/each}}
```

<!--
#### Accepting Input to a Component
-->

#### コンポーネントへの入力の受け付ける

<!--
We want the component to simply provide an input field and yield the results list to its block, so our template will be simple:
-->

コンポーネントには入力フィールドの表示とresultsをブロックに渡す(yield)だけなので、テンプレートは以下のように単純にしておきます。

```app/templates/components/list-filter.hbs
{{input value=value
        key-up=(action 'handleFilterEntry')
        class="light"
        placeholder="Filter By City"}}
{{yield results}}
```

<!--
The template contains an [`{{input}}`](../../templates/input-helpers) helper that renders as a text field, in which the user can type a pattern to filter the list of cities used in a search.
The `value` property of the `input` will be kept in sync with the `value` property in the component.
-->

テンプレートにはテキストフィールドとして描画される[`{{input}}`](../../templates/input-helpers)ヘルパーが含まれています。このヘルパーに都市を絞る条件が入力できます。 `input`の`value`属性は、コンポーネントの`value`属性と同期して保持されます。

<!--
Another way to say this is that the `value` property of `input` is [**bound**](../../object-model/bindings/) to the `value` property of the component.
If the property changes, either by the user typing in the input field, or by assigning a new value to it in our program,
the new value of the property is present in both the rendered web page and in the code.
-->

または、`input`の`value`属性がコンポーネントの`value`属性に[バインド](../../object-model/bindings/) されているとも言えます。 ユーザーが入力フィールドを入力するか、プログラムで新しい値を割り当てるなどして値が変更されると、value属性の変更された値はレンダリングされたWebページとコードの両方に反映されます。

<!--
The `key-up` property will be bound to the `handleFilterEntry` action.
-->

`key-up`属性は、`handleFilterEntry`アクションにバインドされます。

<!--
The `handleFilterEntry` action will apply the search term filter to the list of rentals, and set a component attribute called `results`. The `results` are passed to the `{{yield}}` helper in the template. In the yielded block component, those same `results` are referred to as `|filteredResults|`. Let's apply the filter to our rentals:
-->

`handleFilterEntry`アクションでは、物件一覧を検索ワードで絞り込み、`results`というコンポーネント属性に設定します。
`results`はテンプレートの`{{yield}}`ヘルパーに渡されます。
`list-filter`コンポーネントに渡されるブロックコンポーネントでは、`list-filter`の`results`を`|filteredResults|`という名前で受け取ります。
物件が絞り込まれるようコードを変更しましょう。


```app/components/list-filter.js
import Component from '@ember/component';

export default Component.extend({
  classNames: ['list-filter'],
  value: '',

  init() {
    this._super(...arguments);
    this.get('filter')('').then((results) => this.set('results', results));
  },

  actions: {
    handleFilterEntry() {
      let filterInputValue = this.get('value');
      let filterAction = this.get('filter');
      filterAction(filterInputValue).then((filterResults) => this.set('results', filterResults));
    }
  }

});
```

<!--
#### Filtering Data Based on Input
-->

#### 入力に基づくデータのフィルタリング

<!--
In the above example we use the `init` hook to seed our initial listings by calling the `filter` action with an empty value.
Our `handleFilterEntry` action calls a function called `filter` based on the `value` attribute set by the input helper.
-->

上記の例では、`init`アクションを使用して、`filter`アクションを文字を渡して実行して初期値を生成しています。 `handleFilterEntry`アクションは、inputヘルパーのvalue属性を対象に`filter`という関数を実行しています。

<!--
The `filter` function is passed in by the calling object. This is a pattern known as [closure actions](../../components/triggering-changes-with-actions/#toc_passing-the-action-to-the-component). 
-->

`filter`関数は呼び出し元のオブジェクトによって渡されます。 これは[クロージャーアクション](../../components/triggering-changes-with-actions/#toc_passing-the-action-to-the-component)と呼ばれるパターンです。

<!--
Notice the `then` function called on the result of calling the `filter` function.
The code expects the `filter` function to return a promise.
A [promise](https://www.emberjs.com/api/ember/2.16/classes/Promise) is a JavaScript object that represents the result of an asynchronous function.
A promise may or may not be executed at the time you receive it.
To account for this, it provides functions, like `then` that let you give it code it will run when it eventually does receive a result.
-->

`filter`関数の実行結果に対して、`then`関数が呼び出されたことに注目してください。 このコードでは、`filter`関数がPromiseを返すことを期待しています。 [Promise](https://www.emberjs.com/api/ember/2.16/classes/Promise)は、非同期関数の結果を表すJavaScriptオブジェクトです。 Promiseは、Promiseを受け取った時点で実行される場合もあれば、実行されない場合もあります。 そのため、`then`関数のような、結果を受け取ったときに最終的に実行されるコードを渡せる関数があります。

<!--
To implement the `filter` function to do the actual filter of rentals by city, we'll create a `rentals` controller.
[Controllers](../../controllers/) contain actions and properties available to the template of its corresponding route.
In our case we want to generate a controller called `rentals`.
Ember will know that a controller with the name of `rentals` will apply to the route with the same name.
-->

都市別の物件のフィルタリングを実行する`filter`関数を実装するため、`rentals`コントローラを作成します。 [コントローラ][Controllers](../../controllers/)には、対応するルートのテンプレートで使用できるアクションとプロパティ(属性)が含まれています。 そのためこの場合は、`rentals`というコントローラーを作成することになります。 Emberでは、`rentals`という名前のコントローラーが同じ名前のルートに適用されます。

<!--
Generate a controller for the `rentals` route by running the following:
-->

次のコマンドを実行して、`rentals`ルート用のコントローラを生成します。

```shell
ember g controller rentals
```

<!--
Now, define your new controller like so:
-->

次に、作成されたコントローラを以下のように変更してください。

```app/controllers/rentals.js
import Controller from '@ember/controller';

export default Controller.extend({
  actions: {
    filterByCity(param) {
      if (param !== '') {
        return this.get('store').query('rental', { city: param });
      } else {
        return this.get('store').findAll('rental');
      }
    }
  }
});
```

<!--
When the user types in the text field in our component, the `filterByCity` action in the controller is called.
This action takes in the `value` property, and filters the `rental` data for records in data store that match what the user has typed thus far.
The result of the query is returned to the caller.
-->

ユーザーがコンポーネントのテキストフィールドに入力すると、コントローラーの`filterByCity`アクションが呼び出されます。 このアクションでは、`value`プロパティが使用され、ユーザーが入力した内容と一致する物件データがデータストア内から抽出されます。 クエリの結果は呼び出し元に返されます。

<!--
#### Faking Query Results
-->

#### 検索結果の偽装

<!--
For this action to work, we need to replace our Mirage `config.js` file with the following, so that it can respond to our queries.
Instead of simply returning the list of rentals, our Mirage HTTP GET handler for `rentals` will return rentals matching the string provided in the URL query parameter called `city`. 
-->

この動作を有効にするには、Mirageの`config.js`ファイルを次のように置き換えて、クエリに応答できるようにする必要があります。 単に全物件データを返す代わりに、Mirage HTTP GETハンドラの`rentals`で、`city`というURLクエリパラメータで指定された文字列と一致する物件を返すようにします。

```mirage/config.js{+4,-5,-6,-7,-44,-45,+47,+48,+49,+50,+51,+52,+53,+54,+55,+56,+57}
export default function() {
  this.namespace = '/api';

  let rentals = [{
  this.get('/rentals', function() {
    return {
      data: [{
      type: 'rentals',
      id: 'grand-old-mansion',
      attributes: {
        title: 'Grand Old Mansion',
        owner: 'Veruca Salt',
        city: 'San Francisco',
        "category": 'Estate',
        bedrooms: 15,
        image: 'https://upload.wikimedia.org/wikipedia/commons/c/cb/Crane_estate_(5).jpg',
        description: "This grand old mansion sits on over 100 acres of rolling hills and dense redwood forests."
      }
    }, {
      type: 'rentals',
      id: 'urban-living',
      attributes: {
        title: 'Urban Living',
        owner: 'Mike Teavee',
        city: 'Seattle',
        "category": 'Condo',
        bedrooms: 1,
        image: 'https://upload.wikimedia.org/wikipedia/commons/0/0e/Alfonso_13_Highrise_Tegucigalpa.jpg',
        description: "A commuters dream. This rental is within walking distance of 2 bus stops and the Metro."
      }
    }, {
      type: 'rentals',
      id: 'downtown-charm',
      attributes: {
        title: 'Downtown Charm',
        owner: 'Violet Beauregarde',
        city: 'Portland',
        "category": 'Apartment',
        bedrooms: 3,
        image: 'https://upload.wikimedia.org/wikipedia/commons/f/f7/Wheeldon_Apartment_Building_-_Portland_Oregon.jpg',
        description: "Convenience is at your doorstep with this charming downtown rental. Great restaurants and active night life are within a few feet."
      }
    }];
  };
});

  this.get('/rentals', function(db, request) {
    if(request.queryParams.city !== undefined) {
      let filteredRentals = rentals.filter(function(i) {
        return i.attributes.city.toLowerCase().indexOf(request.queryParams.city.toLowerCase()) !== -1;
      });
      return { data: filteredRentals };
    } else {
      return { data: rentals };
    }
  });
}
```

<!--
After updating our mirage configuration, we should see a simple filter on the home screen that will update the rental list as you type:
-->

Mirageの設定を更新した後、ホーム画面でテキストフィールドに入力すると物件一覧が絞られるようになっています。

![home screen with filter component](../../images/autocomplete-component/styled-super-rentals-filter.png)

<!--
#### Handling Results Coming Back at Different Times
-->

#### 非同期処理と改善

<!--
In our example, you might notice that if you type quickly that the results might get out of sync with the current filter text entered.
This is because our data filtering function is _asynchronous_, meaning that the code in the function gets scheduled for later, while the code that calls the function continues to execute.
Often code that may make network requests is set up to be asynchronous because the server may return its responses at varying times.
-->

テキストフィールドに素早く検索条件を入力すると、入力と同時に結果が反映されていないことに気がつくかもしれません。
これは、検索の機能が_非同期_であるためです。つまり、関数内のコードは後で実行されるようスケジュールされ、引き続きその関数を呼び出しているコードが実行されるためです。 多くの場合、ネットワークリクエストを行う可能性のあるコードは、サーバーの応答時間が様々なため、非同期処理されています。

<!--
Lets add some protective code to ensure our results do not get out of sync with our filter input.
To do this we'll simply provide the filter text to the filter function, so that when the results come back we can compare the original filter value with the current filter value.
We will update the results on screen only if the original filter value and the current filter value are the same.
-->

結果が検索条件との同期が壊れないように、それを防ぐコードを追加しましょう。それにはまず、filter関数に検索条件を一緒に渡します。これにより、結果を取得した時に元の条件と現在の条件を比較できます。元の条件と現在の条件が同じ場合にのみ、画面上の結果を更新します。

```app/controllers/rentals.js{-7,+8,+9,+10,+11,-13,+14,+15,+16,+17}
import Controller from '@ember/controller';

export default Controller.extend({
  actions: {
    filterByCity(param) {
      if (param !== '') {
        return this.get('store').query('rental', { city: param });
        return this.get('store')
          .query('rental', { city: param }).then((results) => {
            return { query: param, results: results };
          });
      } else {
        return this.get('store').findAll('rental');
        return this.get('store')
          .findAll('rental').then((results) => {
            return { query: param, results: results };
          });
      }
    }
  }
});
```

<!--
In the `filterByCity` function in the rentals controller above,
we've added a new property called `query` to the filter results instead of just returning an array of rentals as before.
-->

上のrentalsコントローラの`filterByCity`関数では、以前と同様に物件の配列を返すのではなく、検索結果に`query`という新しいプロパティを追加しました。

```app/components/list-filter.js{-19,-9,+10,+11,+12,+20,+21,+22,+23,+24}
import Component from '@ember/component';

export default Component.extend({
  classNames: ['list-filter'],
  value: '',

  init() {
    this._super(...arguments);
    this.get('filter')('').then((results) => this.set('results', results));
    this.get('filter')('').then((allResults) => {
      this.set('results', allResults.results);
    });
  },

  actions: {
    handleFilterEntry() {
      let filterInputValue = this.get('value');
      let filterAction = this.get('filter');
      filterAction(filterInputValue).then((filterResults) => this.set('results', filterResults));
      filterAction(filterInputValue).then((filterResults) => {
        if (filterResults.query === this.get('value')) {
          this.set('results', filterResults.results);
        }
      });
    }
  }

});
```

<!--
In our list filter component JavaScript, we use the `query` property to compare to the `value` property of the component.
The `value` property represents the latest state of the input field.
Therefore we now check that results match the input field, ensuring that results will stay in sync with the last thing the user has typed.
-->

`list-filter`コンポーネントのJavaScriptでは、`query`プロパティを使用してコンポーネントの`value`プロパティと比較します。
`value`プロパティは、入力フィールドの最新の状態を表します。 
したがって、結果が入力フィールドに一致することを確認することで、結果がユーザーが最後に入力したものと同期した状態を保つようになります。

<!--
While this approach will keep our results order consistent, there are other things to consider when dealing with multiple concurrent tasks,
such as [limiting the number of requests made to the server](https://www.emberjs.com/api/ember/2.16/classes/@ember%2Frunloop/methods/debounce?anchor=debounce).
To create effective and robust autocomplete behavior for your applications,
we recommend considering the [`ember-concurrency`](http://ember-concurrency.com/#/docs/introduction) addon project.
-->

この方法では、結果の順序を一致させることができますが、[サーバーへの要求数を制限する](https://www.emberjs.com/api/ember/2.16/classes/@ember%2Frunloop/methods/debounce?anchor=debounce)など、複数の並行タスクを処理する場合は、他に考慮すべき事項があります。 アプリケーションの効果的で堅牢な補完動作を作成するには、[`ember-concurrency`](http://ember-concurrency.com/#/docs/introduction)アドオンプロジェクトを検討することをお勧めします。

<!--
You can now proceed on to implement the [next feature](../service/), or continue on to test our newly created filter component.
-->

ここで、[次の機能](../service/)の実装に移るか、新しく作成したフィルタコンポーネントのテストの実装に進んでください。

<!--
### An Integration Test
-->

### 統合テスト

<!--
Now that we've created a new component for filtering a list,
we want to create a test to verify it.
Let's use a [component integration test](../../testing/testing-components)
to verify our component behavior,
similar to [how we tested our rental listing component earlier](../simple-component/#toc_an-integration-test).
-->

一覧をフィルタリングするための新しいコンポーネントを作成したので、それを検証するためのテストを作成します。 [rental-listingコンポーネントを以前にテストした](../simple-component/#toc_統合テスト)のと同様に、コンポーネントの動作を確認するために[コンポーネント統合テスト](../../testing/testing-components)を使用しましょう。

<!--
Lets begin by opening the component integration test created when we generated our `list-filter` component, `tests/integration/components/list-filter-test.js`.
Remove the default test, and create a new test that verifies that by default, the component will list all items.
-->

まず、`list-filter`コンポーネントを生成したときに作成されたコンポーネント統合テスト`tests/integration/components/list-filter-test.js`を開きます。 既存のテストを削除し、コンポーネントがデフォルトですべてのアイテムを一覧表示することを確認する新しいテストを作成します。

```tests/integration/components/list-filter-test.js{+8,+9,-11,-12,-13,-14,-15,-16,-17,-18,-19,-20,-21,-22,-23,-24,-25,-26,-27}
import { moduleForComponent, test } from 'ember-qunit';
import hbs from 'htmlbars-inline-precompile';

moduleForComponent('list-filter', 'Integration | Component | filter listing', {
  integration: true
});

test('should initially load all listings', function (assert) {
});

test('it renders', function(assert) {
  // Set any properties with this.set('myProperty', 'value');
  // Handle any actions with this.on('myAction', function(val) { ... });

  this.render(hbs`{{list-filter}}`);

  assert.equal(this.$().text().trim(), '');

  // Template block usage:
  this.render(hbs`
    {{#list-filter}}
      template block text
    {{/list-filter}}
  `);

  assert.equal(this.$().text().trim(), 'template block text');
});
```

<!--
Our list-filter component takes a function as an argument, used to find the list of matching rentals based on the filter string provided by the user.
We provide an action function by setting it to the local scope of our test by calling `this.on`.
-->

list-filterコンポーネントは関数を引数でとり、ユーザーが入力する文字列に基づいて一致する物件を検索に使います。テストのローカルスコープに設定するために`this.on`を使ってアクション関数をセットします。

```tests/integration/components/list-filter-test.js{+3,+5,+6,+13,+14,+15,+16,+17}
import { moduleForComponent, test } from 'ember-qunit';
import hbs from 'htmlbars-inline-precompile';
import RSVP from 'rsvp';

const ITEMS = [{city: 'San Francisco'}, {city: 'Portland'}, {city: 'Seattle'}];
const FILTERED_ITEMS = [{city: 'San Francisco'}];

moduleForComponent('list-filter', 'Integration | Component | filter listing', {
  integration: true
});

test('should initially load all listings', function (assert) {
  // we want our actions to return promises,
  //since they are potentially fetching data asynchronously
  this.on('filterByCity', () => {
    return RSVP.resolve({ results: ITEMS });
  });
});
```

<!--
`this.on` will add the provided function to the test local scope as `filterByCity`, which we can use to provide to the component.
-->

`this.on`は、渡された関数を`filterByCity`としてテストのローカルスコープに追加しています。コンポーネントに渡すのに使用できます。

<!--
Our `filterByCity` function is going to pretend to be the action function for our component, that does the actual filtering of the rental list.
-->

上記の`filterByCity`関数は、実際のコンポーネントのアクションに合わせ、物件検索しているかのように動作させています。

<!--
We are not testing the actual filtering of rentals in this test, since it is focused on only the capability of the component.
We will test the full logic of filtering in acceptance tests, described in the next section. 
-->

このテストでは、コンポーネントの機能のみに焦点を絞っているため、実際の物件のフィルタリングはテストしていません。 次のセクションで説明する受け入れテストでのフィルタリングの完全なロジックをテストします。

<!--
Since our component is expecting the filter process to be asynchronous, we return promises from our filter, using [Ember's RSVP library](https://www.emberjs.com/api/ember/2.16/modules/rsvp).
-->

コンポーネントはフィルタプロセスが非同期であることを期待しているので、[EmberのRSVPライブラリ]
(https://www.emberjs.com/api/ember/2.16/modules/rsvp)を使用してPromiseを返します。

<!--
Next, we'll add the call to render the component to show the cities we've provided above.
-->

次に、コンポーネントをレンダリングする呼び出しを追加して、上に示した都市を表示します。

```tests/integration/components/list-filter-test.js{+19,+20,+21,+22,+23,+24,+25,+26,+27,+28,+29,+30,+31,+32}
import { moduleForComponent, test } from 'ember-qunit';
import hbs from 'htmlbars-inline-precompile';
import RSVP from 'rsvp';

const ITEMS = [{city: 'San Francisco'}, {city: 'Portland'}, {city: 'Seattle'}];
const FILTERED_ITEMS = [{city: 'San Francisco'}];

moduleForComponent('list-filter', 'Integration | Component | filter listing', {
  integration: true
});

test('should initially load all listings', function (assert) {
  // we want our actions to return promises,
  //since they are potentially fetching data asynchronously
  this.on('filterByCity', () => {
    return RSVP.resolve({ results: ITEMS });
  });

  // with an integration test,
  // you can set up and use your component in the same way your application
  // will use it.
  this.render(hbs`
    {{#list-filter filter=(action 'filterByCity') as |results|}}
      <ul>
      {{#each results as |item|}}
        <li class="city">
          {{item.city}}
        </li>
      {{/each}}
      </ul>
    {{/list-filter}}
  `);

});
```

<!--
Finally we add a `wait` call at the end of our test to assert the results.
-->

最後に、テストの最後に`wait`を追加して結果をアサートします。

<!--
Ember's [wait helper](../../testing/testing-components/#toc_waiting-on-asynchronous-behavior)
waits for all asynchronous tasks to complete before running the given function callback.
It returns a promise that we also return from the test.
-->

Emberの[waitヘルパー](../../testing/testing-components/#toc_waiting-on-asynchronous-behavior)は、指定された関数コールバックを実行する前に、すべての非同期タスクが完了するのを待ちます。テストから復帰するPromiseを返します。

<!--
If you return a promise from a QUnit test, the test will wait to finish until that promise is resolved.
In this case our test completes when the `wait` helper decides that processing is finished,
and the function we provide that asserts the resulting state is completed.
-->

QUnitテストでPromiseを返すと、そのPromiseが解決されるまでテストは終了せず待機されます。 下記のコードでは、`wait`ヘルパーの処理の終了の判断と、結果の状態をアサートする関数が完了すると、テストが完了します。

```tests/integration/components/list-filter-test.js{+3,+33,+34,+35,+36}
import { moduleForComponent, test } from 'ember-qunit';
import hbs from 'htmlbars-inline-precompile';
import wait from 'ember-test-helpers/wait';
import RSVP from 'rsvp';

const ITEMS = [{city: 'San Francisco'}, {city: 'Portland'}, {city: 'Seattle'}];
const FILTERED_ITEMS = [{city: 'San Francisco'}];

moduleForComponent('list-filter', 'Integration | Component | filter listing', {
  integration: true
});

test('should initially load all listings', function (assert) {
  // we want our actions to return promises, since they are potentially fetching data asynchronously
  this.on('filterByCity', () => {
    return RSVP.resolve({ results: ITEMS });
  });

  // with an integration test,
  // you can set up and use your component in the same way your application will use it.
  this.render(hbs`
    {{#list-filter filter=(action 'filterByCity') as |results|}}
      <ul>
      {{#each results as |item|}}
        <li class="city">
          {{item.city}}
        </li>
      {{/each}}
      </ul>
    {{/list-filter}}
  `);

  return wait().then(() => {
    assert.equal(this.$('.city').length, 3);
    assert.equal(this.$('.city').first().text().trim(), 'San Francisco');
  });
});
```

<!--
For our second test, we'll check that typing text in the filter will actually appropriately call the filter action and update the listings shown.
-->

2件目のテストでは、入力したテキストが実際にフィルタアクションを適切に呼び出し、表示されている一覧が更新されていることを確認します。

<!--
We'll add some additional functionality to our `filterByCity` action to additionally return a single rental,
represented by the variable `FILTERED_ITEMS` when any value is set.
-->

`filterByCity`アクションにいくつかの追加機能を追加して、値が設定されている時は物件を1件だけ含んだ
`FILTERED_ITEMS`を常に返すようにします。

<!--
We force the action by generating a `keyUp` event on our input field, and then assert that only one item is rendered.
-->

入力フィールドで`keyUp`イベントを生成してアクションを強制的に実行し、次に1つのアイテムだけがレンダリングされることをアサートします。

```tests/integration/components/list-filter-test.js
test('should update with matching listings', function (assert) {
  this.on('filterByCity', (val) => {
    if (val === '') {
      return RSVP.resolve({
        query: val,
        results: ITEMS });
    } else {
      return RSVP.resolve({
        query: val,
        results: FILTERED_ITEMS });
    }
  });

  this.render(hbs`
    {{#list-filter filter=(action 'filterByCity') as |results|}}
      <ul>
      {{#each results as |item|}}
        <li class="city">
          {{item.city}}
        </li>
      {{/each}}
      </ul>
    {{/list-filter}}
  `);

  // The keyup event here should invoke an action that will cause the list to be filtered
  this.$('.list-filter input').val('San').keyup();

  return wait().then(() => {
    assert.equal(this.$('.city').length, 1);
    assert.equal(this.$('.city').text().trim(), 'San Francisco');
  });
});

```

<!--
Now both integration test scenarios should pass.
You can verify this by starting up our test suite by typing `ember t -s` at the command line.
-->

これで、２件の統合テストシナリオはパスするはずです。 確認するには、コマンドラインで`ember t -s`とタイプしてテストスイートを起動します。

<!--
### Acceptance Tests
-->

### 受け入れテスト

<!--
Now that we've tested that the `list-filter` component behaves as expected, let's test that the page itself also behaves properly with an acceptance test.
We'll verify that a user visiting the rentals page can enter text into the search field and narrow the list of rentals by city.
-->

`list-filter`コンポーネントが期待どおりに動作することをテストしたので、ページ自体も受け入れテストで正しく動作することをテストしましょう。 物件一覧ページにアクセスしたユーザーが検索フィールドにテキストを入力し、都市別に物件一覧を絞り込むことができることを確認します。

<!--
Open our existing acceptance test, `tests/acceptance/list-rentals-test.js`, and implement the test labeled "should filter the list of rentals by city".
-->

既存の受け入れテスト、`tests/acceptance/list-rentals-test.js`を開き、"should filter the list of rentals by city"(都市別に物件一覧をフィルタリングする)というラベルのテストを実装します。

```/tests/acceptance/list-rentals-test.js
test('should filter the list of rentals by city.', function (assert) {
  visit('/');
  fillIn('.list-filter input', 'Seattle');
  keyEvent('.list-filter input', 'keyup', 69);
  andThen(function() {
    assert.equal(find('.listing').length, 1, 'should show 1 listing');
    assert.equal(find('.listing .location:contains("Seattle")').length, 1, 'should contain 1 listing with location Seattle');
  });
});
```

<!--
We introduce two new helpers into this test, `fillIn` and `keyEvent`.
-->

このテストでは、`fillIn`と`keyEvent`の2つの新しいヘルパーを使います。

<!--
* The [`fillIn`](http://emberjs.com/api/classes/Ember.Test.html#method_fillIn) helper "fills in" the given text into an input field matching the given selector.
* The [`keyEvent`](http://emberjs.com/api/classes/Ember.Test.html#method_keyEvent) helper sends a key stroke event to the UI, simulating the user typing a key
-->

* [`fillIn`](http://emberjs.com/api/classes/Ember.Test.html#method_fillIn)ヘルパーは、指定されたテキストを指定されたセレクタに一致する入力フィールドに入力します。
* [`keyEvent`](http://emberjs.com/api/classes/Ember.Test.html#method_keyEvent)ヘルパーは、キーストロークイベントをUIに送り、ユーザーのキー入力をシミュレートします。

<!--
In `app/components/list-filter.js`, we have as the top-level element rendered by the component a class called `list-filter`.
We locate the search input within the component using the selector `.list-filter input`,
since we know that there is only one input element located in the list-filter component.
-->

`app/components/list-filter.js`は、コンポーネント内のトップレベルの要素に`list-filter`というクラスを持っています。 `list-filter`コンポーネントにはinput要素が1つしかないことがわかっているので、`.list-filter input`をセレクタに使用してコンポーネント内の検索フィールドの場所を特定できます。

<!--
Our test fills out "Seattle" as the search criteria in the search field,
and then sends a `keyup` event to the same field with a code of `69` (the `e` key) to simulate a user typing.
-->

このテストでは、検索フィールドの検索条件として、Seattle」(シアトル)を入力し、ユーザー入力をシミュレートするためにコード69（eキー）を使用して同じフィールドに`keyup`イベントを送信しています。

<!--
The test locates the results of the search by finding elements with a class of `listing`,
which we gave to our `rental-listing` component in the ["Building a Simple Component"](../simple-component) section of the tutorial.
-->

チュートリアルの[コンポーネントの作成](../simple-component)セクションで`rental-listing`コンポーネントのクラスに`listing`を指定したので、それを要素を検索することで、検索結果の場所を特定しています。

<!--
Since our data is hard-coded in Mirage, we know that there is only one rental with a city name of "Seattle",
so we assert that the number of listings is one and that the location it displays is named, "Seattle".
-->

データはMirageにハードコードしており、都市名が「Seattle(シアトル)」の物件は1件しかないことがわかっていますので、物件数は1件で、表示場所はSeattleであることをアサートしています。

<!--
The test verifies that after filling in the search input with "Seattle", the rental list reduces from 3 to 1,
and the item displayed shows "Seattle" as the location.
-->

入力欄にSeattleを入力すると、物件数が3から1に減少し、表示された項目にの場所がSeattleであることを検証しています。

<!--
You should be down to only 2 failing tests: One remaining acceptance test failure; and our ESLint test that fails on an unused assert for our unimplemented test.
-->

これで失敗しているテストは2件に減っているはずです。実装されていない受け入れテストと未使用のassertで失敗しているESLintテストだけになりました。

![passing acceptance tests](../../images/autocomplete-component/passing-acceptance-tests.png)
