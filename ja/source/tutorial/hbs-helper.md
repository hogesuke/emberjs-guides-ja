<!--
So far, our app is directly showing the user data from our Ember Data models.
As our app grows, we will want to manipulate data further before presenting it to our users.
For this reason, Ember offers Handlebars template helpers to decorate the data in our templates.
Let's use a handlebars helper to allow our users to quickly see if a property is "Standalone" or part of a "Community".
-->

これまでのところ、私たちのアプリはEmber Dataモデルから直接データを表示しています。
アプリを開発していると、ユーザーに表示する前にデータを操作したくなります。
このため、Emberは、テンプレート内のデータを飾るためのHandlebarsテンプレートヘルパーを提供しています。
ハンドルバーヘルパーを使用して、物件が戸建か集合住宅であるかをすぐに確認できるようにしましょう。

<!--
To get started, let's generate a helper for `rental-property-type`:
-->

始めに、`rental-property-type`のヘルパーを生成しましょう：

```shell
ember g helper rental-property-type
```

<!--
This will create two files, our helper and its related test:
-->

これにより、ヘルパーとその関連テストの2つのファイルが作成されます。

```shell
installing helper
  create app/helpers/rental-property-type.js
installing helper-test
  create tests/integration/helpers/rental-property-type-test.js
```

<!--
Our new helper starts out with some boilerplate code from the generator:
-->

作成されたヘルパーのファイルには定型コードが書かれています。

```app/helpers/rental-property-type.js
import { helper } from '@ember/component/helper';

export function rentalPropertyType(params/*, hash*/) {
  return params;
}

export default helper(rentalPropertyType);
```

<!--
Let's update our `rental-listing` component template to use our new helper and pass in `rental.category`:
-->

`rental-listing`コンポーネントのテンプレートを更新して、作成したヘルパーを使用し、`rental.category`を渡しましょう：

```app/templates/components/rental-listing.hbs{-11,+12,+13}
<article class="listing">
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
    <span>Type:</span> {{rental-property-type rental.category}}
      - {{rental.category}}
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
Ideally we'll see "Type: Standalone - Estate" for our first rental property.
Instead, our default template helper is returning back our `rental.category` values.
Let's update our helper to look if a property exists in an array of `communityPropertyTypes`,
if so, we'll return either `'Community'` or `'Standalone'`:
-->

理想的には、最初の賃貸物件には"Type: Standalone - Estate"が表示されるはずなのですが、
代わりに、デフォルトのテンプレートヘルパーが`rental.category`の値を返しています。
`communityPropertyTypes`プロパティが配列に存在するかどうかを調べるためにヘルパーを更新しましょう。
存在する場合は`Community`を、そうでない場合は`Standalone`を返します：

```app/helpers/rental-property-type.js{-3,-4,-5,+7,+8,+9,+10,+11,+13,+14,+15,+16,+18,+19}
import { helper } from '@ember/component/helper';

export function rentalPropertyType(params/*, hash*/) {
  return params;
}

const communityPropertyTypes = [
  'Condo',
  'Townhouse',
  'Apartment'
];

export function rentalPropertyType([propertyType]) {
  if (communityPropertyTypes.includes(propertyType)) {
    return 'Community';
  }

  return 'Standalone';
}

export default helper(rentalPropertyType);
```

<!--
Each [argument](https://guides.emberjs.com/v2.12.0/templates/writing-helpers/#toc_helper-arguments) in the helper will be added to an array and passed to our helper. For example, `{{my-helper "foo" "bar"}}` would result in `myHelper(["foo", "bar"])`. Using array [ES2015 destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) assignment, we can name expected parameters within the array. In the example above, the first argument in the template will be assigned to `propertyType`. This provides a flexible, expressive interface for your helpers, including optional arguments and default values.
-->

ヘルパーの各[引数](https://guides.emberjs.com/v2.12.0/templates/writing-helpers/#toc_helper-arguments)は配列に追加され、ヘルパーに渡されます。 たとえば、 `{{my-helper "foo" "bar"}}`は `myHelper(["foo"、"bar"])`となります。 [ES2015分割代入](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)使用して、配列内に予期されるパラメータの名前を付けることができます。 上記の例では、テンプレートの最初の引数は`propertyType`に割り当てられます。 これにより、オプションの引数やデフォルト値など、柔軟で表現力豊かなヘルパーインタフェースが提供されます。

<!--
Now in our browser we should see that the first rental property is listed as "Standalone",
while the other two are listed as "Community".
-->

ブラウザで、最初の賃貸物件が戸建て(Standalone)としてリストされていることがわかります。
他の2つは集合住宅(Community)としてリストされています。

<!--
### Integration Test
-->

### 統合テスト

<!--
Update the content of the integration test to the following to fix it:
-->

統合テストの内容を次のように更新してください。

```/tests/integration/helpers/rental-property-type-test.js{-9,-10,-11,-17,+12,+13,+18,+21,+22,+23,+24,+25,+26,+27}

import { moduleForComponent, test } from 'ember-qunit';
import hbs from 'htmlbars-inline-precompile';

moduleForComponent('rental-property-type', 'helper:rental-property-type', {
  integration: true
});

// Replace this with your real tests.
test('it renders', function(assert) {
  this.set('inputValue', '1234');
test('it renders correctly for a Standalone rental', function(assert) {
  this.set('inputValue', 'Estate');

  this.render(hbs`{{rental-property-type inputValue}}`);

  assert.equal(this.$().text().trim(), '1234');
  assert.equal(this.$().text().trim(), 'Standalone');
});

test('it renders correctly for a Community rental', function(assert) {
  this.set('inputValue', 'Apartment');

  this.render(hbs`{{rental-property-type inputValue}}`);

  assert.equal(this.$().text().trim(), 'Community');
});
```
