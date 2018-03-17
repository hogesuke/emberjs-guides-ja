<!--
For Super Rentals, we want to be able to display a map showing where each rental is.
To implement this feature, we will take advantage of several Ember concepts:
-->

Super Rentalsで、各物件の場所を地図上に表示できるようにしたいですね。 この機能を実装するために、Emberのいくつかのコンセプトを活用しましょう。

<!--
1. A utility function to create a map from the Google Maps API.
2. A service to keep a cache of rendered maps to use in different places in the application.
3. A component to display a map on each rental listing.
-->

1. Google Maps APIから地図を作成するユーティリティ関数
2. 描画されたマップをキャッシュし、アプリケーションのさまざまな場所で使用するためのサービス
3. 各物件情報に地図を表示するコンポーネント

<!--
### Making Google Maps Available
-->

### Googleマップを利用できるようにする

<!--
Before implementing a map, we need to make a 3rd party map API available to our Ember app.
There are several ways to include 3rd party libraries in Ember.
See the guides section on [managing dependencies](../../addons-and-dependencies/managing-dependencies/)
as a starting point when you need to add one.
-->

マップを実装する前に、EmberアプリでサードパーティのマップAPIを利用できるようにする必要があります。 Emberにサードパーティライブラリを含めるにはいくつかの方法があります。詳しくは [依存関係の管理](../../addons-and-dependencies/managing-dependencies/)を参照してください。

<!--
The [Google Maps API](https://developers.google.com/maps/documentation/javascript/tutorial) requires us to reference its library from a remote script.
In this case we'll provide this script to our Ember app via an Addon called `ember-simple-google-maps`.
-->

[Google Maps API](https://developers.google.com/maps/documentation/javascript/tutorial)は、外部スクリプトからライブラリを参照する必要があります。 今回は、`ember-simple-google-maps`というアドオンを経由してその外部スクリプトを利用します。

```shell
ember install ember-simple-google-maps
```

<!--
Google Maps requires an API key for deployment.
You can [Generate an API key](https://developers.google.com/maps/documentation/javascript/get-api-key)
from Google.
Add your new API key to the application by stopping the server and restarting it with the environment variable, `GOOGLE_MAPS_API_KEY`.
-->

Googleマップには、導入にAPIキーが必要です。 [GoogleからAPIキーを生成する](https://developers.google.com/maps/documentation/javascript/get-api-key)ことができます。 新しいAPIキーをアプリケーションに追加するには、サーバーを停止し、`GOOGLE_MAPS_API_KEY`環境変数を設定してサーバーを再起動します。

```shell
GOOGLE_MAPS_API_KEY=<your key here> ember s
```

<!--
### Accessing the Google Maps API with a Utility
-->

### ユーティリティを使用したGoogle Maps APIへのアクセス

<!--
Ember utilities are reusable code that can be accessed from various parts of the application.
For Super Rentals, we'll use a utility to access the Google Maps API.
The utility will abstract the Google API away from our Maps service,
which will allow for future reuse of the maps API within the application,
easier refactoring to alternate maps implementations,
and easier testing of code that depends on it.
-->

Emberでは、ユーティリティはアプリケーションのさまざまな部分からアクセスできる再利用可能なコードです。
Super Rentalsでは、ユーティリティを使用してGoogle Maps APIにアクセスします。
ユーティリティではGoogle APIをMapsサービスに抽象化します。
これにより、アプリケーション内でマップAPIを再利用したり、他のマップに替える時のリファクタリングの容易化、およびそれに依存するコードのテストがしやすくなります。

<!--
Now that we have the maps API available to the application, we can create our map utility.
Utility files can be generated using Ember CLI.
-->

アプリケーションでマップAPIを使用できるようになったので、マップユーティリティの作成に取り掛かりましょう。
ユーティリティファイルは、Ember CLIを使用して生成することができます。

```shell
ember g util google-maps
```

<!--
The CLI `generate util` command will create a utility file and a unit test.
We'll delete the unit test since we don't want to test Google code.
-->

CLIの`generate util`コマンドはユーティリティファイルとユニットテストを作成します。
単体テストは、Googleコードをテストしたくないので削除します。

<!--
Our app needs a single function, `createMap`,
which makes use of `google.maps.Map` to create our map element,
`google.maps.Geocoder` to lookup the coordinates of our location,
and `google.maps.Marker` to pin our map based on the resolved location.
-->

`google.maps.Map`を使って地図を作成し、
`google.maps.Geocoder`で場所の座標を検索し、
`google.maps.Marker`をその座標にピンを置く`createMap`関数を作り、アプリケーションで利用します。

```app/utils/google-maps.js{-1,-2,-3,+4,+6,+8,+10,+11,+12,+14,+15,+16,+17,+18,+20,+21,+22,+23,+24,+25,+26,+27,+28,+29,+31}
export default function googleMaps() {
  return true;
}
import EmberObject from '@ember/object';

const google = window.google;

export default EmberObject.extend({

  init() {
    this.set('geocoder', new window.google.maps.Geocoder());
  },

  createMap(element, location) {
    let map = new google.maps.Map(element, { scrollwheel: false, zoom: 10 });
    this.pinLocation(location, map);
    return map;
  },

  pinLocation(location, map) {
    this.get('geocoder').geocode({address: location}, (result, status) => {
      if (status === google.maps.GeocoderStatus.OK) {
        let geometry = result[0].geometry.location;
        let position = { lat: geometry.lat(), lng: geometry.lng() };
        map.setCenter(position);
        new google.maps.Marker({ position, map, title: location });
      }
    });
  }

});
```

<!--
### Fetching Maps With a Service
-->

### サービスを使って地図を取得する

<!--
Now that we are able to generate a map element,
we will implement a maps service that will keep a reference to the Map object we create,
and attach the map to an element in our application
-->

これでマップ要素を生成できるようになりました。マップサービスを実装して、作成したMapオブジェクトへの参照を保持し、アプリケーションの要素にマップを添付します

<!--
Accessing our maps API through a [service](../../applications/services) will give us several benefits
-->

[サービス](../../applications/services)を使った地図APIへのアクセスは、いくつか利点があります。

<!--
* It is injected with a [service locator](https://en.wikipedia.org/wiki/Service_locator_pattern),
  meaning it will abstract the maps API from the code that uses it,
  allowing for easier refactoring and maintenance.
* It is lazy-loaded, meaning it won't be initialized until it is called the first time.
  In some cases this can reduce your app's processor load and memory consumption.
* It is a singleton, which means there is only one instance of the service object in the browser.
  This will allow us to keep map data while the user navigates around the app,
  so that returning to a page doesn't require it to reload its maps.
-->

* [サービスロケータ](https://en.wikipedia.org/wiki/Service_locator_pattern)とともにインジェクトされるため、マップAPIを使用するコードからマップAPIを抽象化し、より簡単なリファクタリングとメンテナンスが可能になります。
* 遅延ロードされているため、最初に呼び出されるまで初期化されません。 場合によっては、これによりアプリのプロセッサ負荷とメモリ消費量が減少する可能性があります。
* シングルトンであるため、ブラウザにはサービスオブジェクトのインスタンスが1つしかありません。 これにより、ユーザーがアプリを開いている間は地図データを保持できるため、ページに戻る際に地図を再読み込みする必要はありません。

<!--
Let's get started creating our service by generating it through Ember CLI,
which will create the service file, as well as a unit test for it.
-->

Ember CLIを使用してサービスを作成し、サービスファイルを作成してみましょう。また、サービスファイルの単体テストも作成します。

```shell
ember g service maps
```

<!--
Now implement the service as follows.
Note that we check if a map already exists for the given location and use that one,
otherwise we call a Google Maps utility to create one.
-->

次のようにサービスを実装していきます。
特定の場所のマップが既に存在するかどうかを確認し、あればそのマップを使用します。
そうでない場合は、Google Mapsユーティリティを呼び出してマップを作成します。

```app/services/maps.js{+2,+3,+5,+9,+10,+11,+12,+13,+14,+15,+16,+18,+19+,+20,+21,+22,+23,+24,+25,+26,+27,+29,+30,+31,+32,+33}
import Service from '@ember/service';
import { camelize } from '@ember/string';
import EmberObject from '@ember/object';

import MapUtil from '../utils/google-maps';

export default Service.extend({

  init() {
    if (!this.get('cachedMaps')) {
      this.set('cachedMaps', EmberObject.create());
    }
    if (!this.get('mapUtil')) {
      this.set('mapUtil', MapUtil.create());
    }
  },

  getMapElement(location) {
    let camelizedLocation = camelize(location);
    let element = this.get(`cachedMaps.${camelizedLocation}`);
    if (!element) {
      element = this.createMapElement();
      this.get('mapUtil').createMap(element, location);
      this.set(`cachedMaps.${camelizedLocation}`, element);
    }
    return element;
  },

  createMapElement() {
    let element = document.createElement('div');
    element.className = 'map';
    return element;
  }

});
```

<!--
### Display Maps With a Component
-->

### コンポーネントで地図を表示する

<!--
With a service and utility that render a map to a web page element,
we'll connect it to our application using a component.
-->

ページ内に地図を描画するサービスとユーティリティは、
コンポーネントを使用してアプリケーションと繋ぎます。

<!--
Generate the map component using Ember CLI.
-->

Ember CLIを使用して地図コンポーネントを生成します。

```shell
ember g component location-map
```

<!--
Running this command generates three files: a component JavaScript file, a template, and a test file.
-->

このコマンドを実行すると、コンポーネントのJavaScriptファイル、テンプレート、テストファイルの3つのファイルが生成されます。

<!--
Let's start by adding a `div` element to the component template.
This `div` will act as a place for the 3rd party map API to render the map to.
-->

コンポーネントテンプレートに`div`要素を追加することから始めましょう。
この`div`は、サードパーティのマップAPIがマップを描画する場所として使います。

```app/templates/components/location-map.hbs{-1,+2}
{{yield}}
<div class="map-container"></div>
```

<!--
Next, update the component to append the map output to the `div` element we created.
-->

次に、コンポーネントを更新して、マップ出力を追加した`div`要素に追加します。

<!--
We provide the maps service into our component by initializing a property of our component, called `maps`.
Services are commonly made available in components and other Ember objects by ["service injection"](../../applications/services/#toc_accessing-services).
When you initialize a property with `import { inject } from '@ember/service';`,
Ember tries to set that property with a service matching its name.
-->

`maps`と呼ばれるコンポーネントのプロパティを初期化することによって、マップサービスをコンポーネントに提供します。
サービスは通常、コンポーネントや他のEmberオブジェクトで[サービスインジェクション]によって利用可能になります。
`import { inject } from '@ember/service';`でプロパティを初期化すると、
Emberはその名前に一致するサービスでそのプロパティを設定しようとします。

<!--
With our `maps` service, our component will call the `getMapElement` function with the provided location.
We append the map element we get back from the service by implementing `didInsertElement`,
which is a [component lifecycle hook](../../components/the-component-lifecycle/#toc_integrating-with-third-party-libraries-with-code-didinsertelement-code).
This function runs during the component render, after the component's markup gets inserted into the page.
-->

`maps`サービスでは、コンポーネントは指定された場所で`getMapElement`関数を呼び出します。
コンポーネントの[ライフサイクルフック](../../components/the-component-lifecycle/#toc_integrating-with-third-party-libraries-with-code-didinsertelement-code)である`didInsertElement`を実装することによって、サービスから取得したmap要素を追加します。
この関数は、コンポーネントのマークアップがページに挿入後、コンポーネントのレンダリング中に実行されます。

```app/components/location-map.js{+2,+5,+7,+8,+9,+10,+11,+12}
import Component from '@ember/component';
import { inject as service } from '@ember/service';

export default Component.extend({
  maps: service(),

  didInsertElement() {
    this._super(...arguments);
    let location = this.get('location');
    let mapElement = this.get('maps').getMapElement(location);
    this.$('.map-container').append(mapElement);
  }
});
```

<!--
You may have noticed that `this.get('location')` refers to a property location we haven't defined.
This property will be passed in to the component by its parent template below.
-->

`this.get('location')`は、定義していないlocationプロパティを参照していることに気づいたかもしれません。 このプロパティは、下の親テンプレートによってコンポーネントに渡されます。

<!--
Finally open the template file for our `rental-listing` component and add the new `location-map` component.
-->

最後に、`rental-listing`コンポーネント用のテンプレートファイルを開き、`location-map`コンポーネントを追加します。

```app/templates/components/rental-listing.hbs{+20}
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
    <span>Type:</span> {{rental-property-type rental.category}}
      - {{rental.category}}
  </div>
  <div class="detail location">
    <span>Location:</span> {{rental.city}}
  </div>
  <div class="detail bedrooms">
    <span>Number of bedrooms:</span> {{rental.bedrooms}}
  </div>
  {{location-map location=rental.city}}
</article>
```

<!--
After starting the server we should now see some end to end maps functionality show up on our front page!
-->

サーバーを起動した後、地図が表示されているのを確認してください。

![super rentals homepage with maps](../../images/service/style-super-rentals-maps.png)

<!--
You may now either move onto the [next feature](../subroutes/), or continue here to test the maps feature we just added.
-->

ここから先は、[次の機能の実装](../subroutes/)に移動するか、引き続き追加したばかりの地図機能のテストを実装してください。

<!--
###  Unit testing a Service
-->

### サービスの単体テスト

<!--
We'll use a unit test to validate the service.
Unit tests are more isolated than integration tests and application tests,
and are intended for testing specific logic within a class.
-->

単体テストを使用してサービスを検証します。
単体テストは統合テストやアプリケーションテストよりも分離されており、クラス内の特定のロジックをテストするためのものです。

<!--
For our service unit test, we'll want to verify that locations that have been previously loaded are fetched from cache, while new locations are created using the utility.
We will isolate our tests from actually calling Google Maps by stubbing our map utility.
On line 6 of `maps-test.js` below we create an Ember object to simulate the behavior of the utility, but instead of creating a google map, we return an empty JavaScript object.
-->

サービスの単体テストでは、以前にロードされた場所はキャッシュからフェッチされ、新しい場所はユーティリティを使用して作成されることを検証します。
マップユーティリティをスタブして、実際にGoogleマップを呼び出さないようテストを分離します。
下の`maps-test.js`の6行目では、ユーティリティの動作をシミュレートするEmberオブジェクトを作成しますが、Googleマップを作成する代わりに空のJavaScriptオブジェクトを返します。

<!--
To instantiate the service, we can instantiate it through ember's resolver using the [`factoryFor`](https://emberjs.com/api/ember/release/classes/ApplicationInstance/methods/factoryFor?anchor=factoryFor) method.
`factoryFor` allows us to have control over the creation of the service in Ember, to pass arguments to the constructor that can override parts of the service for our tests.
-->

サービスをインスタンス化するには、[`factoryFor`](https://emberjs.com/api/ember/release/classes/ApplicationInstance/methods/factoryFor?anchor=factoryFor)メソッドを使用してEmberのリゾルバを使用してサービスをインスタンス化できます。
`factoryFor`はEmberでサービスの作成を制御し、テストのためにサービスの一部をオーバーライドできる引数をコンストラクタに渡すことができます。

<!--
For cases where we do not need to override parts of the service, we can use [`lookup`](https://emberjs.com/api/ember/release/classes/ApplicationInstance/methods/lookup?anchor=lookup)
In our test below we are passing in our fake map utility object in the first test, and passing a cache object for the second test.
-->

サービスの一部をオーバーライドする必要がない場合は、[`lookup`](https://emberjs.com/api/ember/release/classes/ApplicationInstance/methods/lookup?anchor=lookup)を使用できます。
以下のテストでは、最初のテストでは偽のマップユーティリティオブジェクトを渡し、2番目のテストではキャッシュオブジェクトを渡します。

```tests/unit/services/maps-test.js{+4,+5,-7,+8,+10,-11,-12,-13,-14,-15,-16,+17,+18,+19,+20,+21,+22,+23,+24,+25,+26,+27,+28,+29,+30,+31,+32,+33,+34,+35,+36,+37,+38,+39,+40,+41}
import { module, test } from 'qunit';
import { setupTest } from 'ember-qunit';


const DUMMY_ELEMENT = {};

module('Unit | Service | maps', function(hooks) {
module('service:maps', 'Unit | Service | maps', function (hooks) {
  setupTest(hooks);

  // Replace this with your real tests.
  test('it exists', function(assert) {
    let service = this.owner.lookup('service:maps');
    assert.ok(service);
  });

  test('should create a new map if one isnt cached for location', function (assert) {
    assert.expect(4);
    let stubMapUtil = {
      createMap(element, location) {
        assert.ok(element, 'createMap called with element');
        assert.ok(location, 'createMap called with location');
        return DUMMY_ELEMENT;
      }
    };
    let mapService = this.owner.factoryFor('service:maps').create({ mapUtil: stubMapUtil });
    let element = mapService.getMapElement('San Francisco');
    assert.ok(element, 'element exists');
    assert.equal(element.className, 'map', 'element has class name of map');
  });

  test('should use existing map if one is cached for location', function (assert) {
    assert.expect(1);
    let stubCachedMaps = {
      sanFrancisco: DUMMY_ELEMENT
    };
    let mapService = this.owner.factoryFor('service:maps').create({ cachedMaps: stubCachedMaps });
    let element = mapService.getMapElement('San Francisco');
    assert.equal(element, DUMMY_ELEMENT, 'element fetched from cache');
  });

})
```

<!--
When the service calls `createMap` on our fake utility `stubMapUtil`, we will run asserts to validate that it is called.
In our first test notice that we expect four asserts to be run in line 18. Two of the asserts run in the test function, while the other two are run when `createMap` is called.
-->

サービスが`createMap`を実行すると、偽のユーティリティ``stubMapUtil`呼び出されたことを検証するためにアサートを実行します。
最初のテストでは、17行目で4つのアサーションが実行されることがわかります。2つのアサーションはテスト関数で実行され、残りの2つは`createMap`が呼び出されたときに実行されます。

<!--
In the second test, only one assert is expected (line 33), since the map element is fetched from cache and does not use the utility.
-->

2番目のテストでは、マップ要素がキャッシュからフェッチし、ユーティリティを使用しないため、アサートは1つだけです（33行目）。

<!--
Also, note that the second test uses a dummy object as the returned map element (defined on line 5).
Our map element can be substituted with any object because we are only asserting that the cache has been accessed (see line 39).
-->

また、2番目のテストでは、返されたマップ要素（5行目で定義）としてダミーオブジェクトが使用されています。
地図要素は、キャッシュにアクセスしたことを主張するだけなので、どのオブジェクトでも置き換えることができます(39行目を参照)。

<!--
The location in the cache has been [`camelized`](https://www.emberjs.com/api/ember/release/classes/String/methods/camelize?anchor=camelize) (line 30),
so that it may be used as a key to look up our element.
This matches the behavior in `getMapElement` when city has not yet been cached.
-->

キャッシュ内の都市名は[`camelized`](https://www.emberjs.com/api/ember/release/classes/String/methods/camelize?anchor=camelize)されています（30行目）。
その要素を調べるための鍵として使用することができます。
これは、都市がまだキャッシュされていないときの`getMapElement`の振る舞いと合わせています。

<!--
### Integration Testing the Map Component
-->

### マップコンポーネントの統合テスト

<!--
Now let's test that the map component is relying on our service to provide map elements.
-->

マップコンポーネントがGoogleのサービスに依存して地図要素を提供していることをテストしましょう。

<!--
To limit the test to validating only its own behavior and not the service, we'll take advantage of the registration API to register a stub maps service.
That way when Ember injects the map service into the component, it uses our fake service instead of the real one.
-->

サービスではなく、コンポーネントの動作のみを検証するように制限するため、サービスインジェクションをいかしてスタブを注入します。
Emberがマップサービスをコンポーネントに注入すると、実際のサービスの代わりに偽のサービスが使用されます。

<!--
A stub stands in place of the real object in your application and simulates its behavior.
In the stub service, define a method that will fetch the map based on location, called `getMapElement`.
-->

アプリケーション内の実際のオブジェクトの代わりにスタブを使って、その動作をシミュレートします。
スタブに、位置に基づいてマップをフェッチする`getMapElement`というメソッドを定義します。

```tests/integration/components/location-map-test.js{+1,+7,+8,+9,+10,+11,+12,+13,+14,+19,+20,+21,+22,+24,+25,+26,+27,+28,+29,-31,-32,-33,-34,-35,-36,-37,-38,-39,-40,-41,-42,-43,-44,-45,-46,-47}
import Service from '@ember/service';
import { module, test } from 'qunit';
import { setupRenderingTest } from 'ember-qunit';
import { render } from '@ember/test-helpers';
import hbs from 'htmlbars-inline-precompile';

const StubMapsService = Service.extend({
  getMapElement(location) {
    this.set('calledWithLocation', location);
    // We create a div here to simulate our maps service,
    // which will create and then cache the map element
    return document.createElement('div');
  }
});

module('Integration | Component | location map', function(hooks) {
  setupRenderingTest(hooks);

  hooks.beforeEach(function() {
    this.owner.register('service:maps', StubMapsService);
    this.mapsService = this.owner.lookup('service:maps');
  });

  test('should append map element to container element', async function(assert) {
    this.set('myLocation', 'New York');
    await render(hbs`{{location-map location=myLocation}}`);
    assert.equal(this.element.querySelector('.map-container').childNodes.length, 1, 'container should have one child');
    assert.equal(this.get('mapsService.calledWithLocation'), 'New York', 'should call service with New York');
  });

  test('it renders', async function(assert) {
    // Set any properties with this.set('myProperty', 'value');
    // Handle any actions with this.set('myAction', function(val) { ... });

    await render(hbs`{{rental-listing}}`);

    assert.equal(this.element.textContent.trim(), '');

    // Template block usage:
    await render(hbs`
      {{#location-map}}
        template block text
      {{/location-map}}
    `);

    assert.equal(this.element.textContent.trim(), 'template block text');
  });

});
```

<!--
In the `beforeEach` function that runs before each test, we use the built-in function `this.register` to [register](../../applications/dependency-injection/#toc_factory-registrations) our stub service in place of the maps service.
Registration makes an object available to your Ember application for things like loading components from templates and injecting services in this case.
-->

各テストの前に実行される`beforeEach`関数では、組み込み関数`this.register`を使用して、マップサービスの代わりにスタブサービスを[register](../../applications/dependency-injection/#toc_factory-registrations)(登録)します。
この場合、登録により、テンプレートからコンポーネントをロードしたり、サービスをインジェクトするなどの目的でEmberアプリケーションでオブジェクトを使用できるようになります。

<!--
The call to the function `this.inject.service` [injects](../../applications/dependency-injection/#toc_ad-hoc-injections) the service we just registered into the context of the tests, so each test may access it through `this.get('mapsService')`.
In the example we assert that `calledWithLocation` in our stub is set to the location we passed to the component.
-->

`this.inject.service`関数の呼び出しは、登録したサービスをテストのコンテキストに[inject](../../applications/dependency-injection/#toc_ad-hoc-injections)(注入)するので、各テストは`this.get('mapsService')`でアクセスすることができます。
この例では、スタブ内の`calledWithLocation`が、コンポーネントに渡された位置に設定されていると主張しています。


<!--
### Stubbing Services in Application Tests
-->

### アプリケーションでサービスをスタブする

<!--
Finally, we want to update our application tests to account for our new service.
While it would be great to verify that a map is displaying, we don't want to hammer the Google Maps API every time we run our application test.
For this tutorial we'll rely on our component integration tests to ensure that the map DOM is being attached to our screen.
To avoid hitting our Maps request limit, we'll stub out our Maps service in our application tests.
-->

最後に、作成したサービスを考慮してアプリケーションテストを更新します。
地図が表示されていることを確認することは素晴らしいことですが、アプリケーションテストを実行するたびにGoogle Maps APIをドンドン叩きたくありません。
このチュートリアルでは、マップのDOMが画面にアタッチされているかは、コンポーネントの統合テストに任せます。
アプリケーションテストでは、マップリクエストの制限を超えないように、Mapsサービスをスタブします。

<!--
Often, services connect to third party APIs that are not desirable to include in automated tests.
To stub these services we simply have to register a stub service that implements the same API, but does not have the dependencies that are problematic for the test suite.
-->

多く場合、第三者のAPIに接続するサービスを、自動テストに含めることは望ましくありません。
これらのサービスのスタブは、同じAPIを実装するスタブサービスを登録するだけで済み、テストスイートに問題のある依存関係が入ることはありません。

<!--
Add the following code to your application test
-->

アプリケーションテストに次のコードを追加します。

```/tests/acceptance/list-rentals-test.js{+1,+13,+14,+15,+16,+17,+23,+24,+25}
import Service from '@ember/service';
import { module, test } from 'qunit';
import { setupApplicationTest } from 'ember-qunit';
import setupMirage from 'ember-cli-mirage/test-support/setup-mirage';
import {
  click,
  currentURL,
  visit,
  fillIn,
  triggerKeyEvent
} from '@ember/test-helpers'

let StubMapsService = Service.extend({
  getMapElement() {
    return document.createElement('div');
  }
});

module('Acceptance | list rentals', function(hooks) {
  setupApplicationTest(hooks);
  setupMirage(hooks);

  hooks.beforeEach(function() {
    this.owner.register('service:maps', StubMapsService);
  });
  ...
});
```

<!--
What's happening here is we are adding our own stub maps service that simply creates an empty div.
Then we are putting it in Ember's [registry](../../applications/dependency-injection#toc_factory-registrations) using the [owner](https://emberjs.com/api/ember/3.0/functions/@ember%2Fapplication/getOwner) object given by the test context. When our component loads the maps service, it gets our stub service instead.
That way every time that component is created, our stub map service gets injected over the Google maps service.
Now when we run our application tests, you'll notice that maps do not get rendered as the test runs.
-->

ここでは、空のdivを作成する独自のマップサービスのスタブを追加しています。
次に、テストコンテキストから与えられる[owner](https://emberjs.com/api/ember/3.0/functions/@ember%2Fapplication/getOwner)オブジェクトを使って、Emberの[レジストリ](../../applications/dependency-injection#toc_factory-registrations)にスタブを登録します。
コンポーネントがマップサービスをロードする時に、スタブが代わりにロードされるようになります。
これにより、コンポーネントが作成されるたびに、マップサービスのスタブがGoogleマップサービスの代わりに注入されます。
アプリケーションテストを実行すると、テスト中はマップが描画されていないことがわかります。

![application tests without maps](../../images/service/acceptance-without-maps.png)
