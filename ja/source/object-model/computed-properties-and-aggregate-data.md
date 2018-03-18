<!--
When a computed property depends on the contents of an array, there are a few
extra methods you'll need to use in order to correctly recognize when the
contents of the array change. Arrays have two special keys you can append to
array properties to track changes on them, `[]` and `@each`.
-->

算出プロパティが配列の内容に依存する場合、配列の内容がいつ変化するかを正しく認識するためのメソッドを使用します。
配列には、配列プロパティに追加できる2つの特殊なキーがあり、配列の変更を`[]`と`@each`で追跡できます。

## `[]`

<!--
Sometimes a computed property needs to update when items are added to, removed from, or replaced in an array.
In those cases we can use the `[]` array key to tell the property to update at the right time.
We'll use the familiar todo list for our examples:
-->

時々、算出プロパティは、配列に項目を追加、削除、または置換した時に更新される必要があります。
その場合は`[]`配列キーを使用して、プロパティを適切なタイミングで更新するように指示できます。
以下は、よくあるtodoリストの例です。

```app/components/todo-list.js
import EmberObject, { computed } from '@ember/object';
import Component from '@ember/component';

export default Component.extend({
  todos: null,

  init() {
    this.set('todos', [
      EmberObject.create({ title: 'Buy food', isDone: true }),
      EmberObject.create({ title: 'Eat food', isDone: false }),
      EmberObject.create({ title: 'Catalog Tomster collection', isDone: true }),
    ]);
  },

  titles: computed('todos.[]', function() {
    let todos = this.get('todos');
    return todos.mapBy('title');
  })
});
```

<!--
The dependent key `todos.[]` instructs Ember.js to update bindings
and fire observers when any of the following events occurs:
-->

依存キー`todos.[]`は、Ember.jsに以下のイベントが発生した時にバインディングを更新し、オブザーバーを起動するように指示します。

<!--
1. The `todos` property of the component is changed to a different array.
2. An item is added to the `todos` array.
3. An item is removed from the `todos` array.
4. An item is replaced in the `todos` array.
-->

1. コンポーネントの `todos`プロパティが別の配列に変更された。
2. `todos`配列に項目が追加された。
3. `todos`配列から項目が削除された。
4. `todos`配列の項目が置き換えられた。

<!--
Notably, the computed property will not update if an individual todo is mutated.
For that to happen, we need to use the special `@each` key.
-->

注意すべき点は、個々のtodoが変更された場合、プロパティは更新されないことです。
個々の更新を追跡するには、`@each`キーを使用する必要があります。

## `@each`

<!--
Sometimes you have a computed property whose value depends on the properties of
items in an array. For example, you may have an array of todo items, and want
to calculate the incomplete todo's based on their `isDone` property.
-->

算出プロパティが、配列内のオブジェクトのプロパティに依存する時もあります。
たとえば、ToDoの配列があり、その`isDone`プロパティに基づいて未完了のToDoを算出する場合です。

<!--
To facilitate this, Ember provides the `@each` key illustrated below:
-->

これを容易にするため、Emberには以下に示す`@each`キーがあります。

```app/components/todo-list.js
import EmberObject, { computed } from '@ember/object';
import Component from '@ember/component';

export default Component.extend({
  todos: null,

  init() {
    this.set('todos', [
      EmberObject.create({ isDone: true }),
      EmberObject.create({ isDone: false }),
      EmberObject.create({ isDone: true }),
    ]);
  },

  incomplete: computed('todos.@each.isDone', function() {
    let todos = this.get('todos');
    return todos.filterBy('isDone', false);
  })
});
```

<!--
Here, the dependent key `todos.@each.isDone` instructs Ember.js to update bindings
and fire observers when any of the following events occurs:
-->

上記のコードでは、依存キー`todos.@each.isDone`は、Ember.jsに以下のイベントが発生した時にバインディングを更新しオブザーバーが作動するように指示してします。

<!--
1. The `todos` property of the component is changed to a different array.
2. An item is added to the `todos` array.
3. An item is removed from the `todos` array.
4. An item is replaced in the `todos` array.
5. The `isDone` property of any of the objects in the `todos` array changes.
-->

1. コンポーネントの`todos`プロパティが別の配列に変更された。
2. `todos`配列に項目が追加された。
3. `todos`配列から項目が削除された。
4. `todos`配列の項目が置き換えられた。
5. `todos`配列内のいずれかのオブジェクトの`isDone`プロパティが変化した。

<!--
### Multiple Dependent Keys
-->

### 複数の依存キー

<!--
It's important to note that the `@each` key can be dependent on more than one key.
For example, if you are using `Ember.computed` to sort an array by multiple keys,
you would declare the dependency with braces: `todos.@each.{priority,title}`
-->

`@each`キーが複数のキーに依存できることを知っておくことは大事です。
たとえば、`Ember.computed`を使用して複数のキーで配列をソートする場合、中括弧を使用して依存関係を`todos.@each.{priority,title}`のように宣言できます。

<!--
### When to use `[]` and `@each`
-->

### `[]`と`@each`をいつ使うか

```
Both `[]` and `@each` will update bindings when the array is replaced and when the members of the
array are changed.  If you're using `@each` on a particular property, you don't also need to use `[]`:
```

`[]`と`@each`のどちらも、配列が置換された時、および配列のメンバーが変更された時にバインディングを更新します。
特定のプロパティで`@each`を使用している場合は、`[]`を使用する必要はありません。

<!--
```javascript
  //specifying both '[]' and '@each' is redundant here
  incomplete: computed('todos.[]', 'todos.@each.isDone', function() {
  ...
  })
```
-->

```javascript
  // '[]'と'@each'の両方を指定することは、以下のケースでは冗長です
  incomplete: computed('todos.[]', 'todos.@each.isDone', function() {
  ...
  })
```

<!--
Using `@each` is more expensive than `[]`, so default to `[]` if you don't actually have to observe property
changes on individual members of the array.
-->

`@each`の使用は`[]`よりも負荷が高いため、配列の個々のメンバーのプロパティの変更を監視する必要がない場合は`[]`を使いましょう。

<!--
### Computed Property Macros
-->

### 算出プロパティマクロ

<!--
Ember also provides a computed property macro
[`computed.filterBy`](https://www.emberjs.com/api/ember/release/classes/@ember%2Fobject%2Fcomputed/methods/alias?anchor=filterBy),
which is a shorter way of expressing the above computed property:
-->

また、Emberには算出プロパティマクロ[`computed.filterBy`](https://www.emberjs.com/api/ember/release/classes/@ember%2Fobject%2Fcomputed/methods/alias?anchor=filterBy)があります。
これにより、上記の算出プロパティをより短く表現できます。

```app/components/todo-list.js
import EmberObject, { computed } from '@ember/object';
import { filterBy } from '@ember/object/computed';
import Component from '@ember/component';

export default Component.extend({
  todos: null,

  init() {
    this.set('todos', [
      EmberObject.create({ isDone: true }),
      EmberObject.create({ isDone: false }),
      EmberObject.create({ isDone: true }),
    ]);
  },

  incomplete: filterBy('todos', 'isDone', false)
});
```

<!--
In both of the examples above, `incomplete` is an array containing the single incomplete todo:
-->

上記の例では、`incomplete`は1件の未完了のtodoを含む配列になります。

```javascript
import TodoListComponent from 'app/components/todo-list';

let todoListComponent = TodoListComponent.create();
todoListComponent.get('incomplete.length');
// 1
```

<!--
If we change the todo's `isDone` property, the `incomplete` property is updated
automatically:
-->

todoの`isDone`プロパティを変更すると、`incomplete`プロパティが自動的に更新されます。

```javascript
import EmberObject from '@ember/object';

let todos = todoListComponent.get('todos');
let todo = todos.objectAt(1);
todo.set('isDone', true);

todoListComponent.get('incomplete.length');
// 0

todo = EmberObject.create({ isDone: false });
todos.pushObject(todo);

todoListComponent.get('incomplete.length');
// 1
```

<!--
Note that `@each` only works one level deep. You cannot use nested forms like
`todos.@each.owner.name` or `todos.@each.owner.@each.name`.
-->

`@each`は一階層までしか使えないことに注意してください。
`todos.@each.owner.name`や`todos.@each.owner.@each.name`といったネストした形式は使用できません。

<!--
## `[]` vs `@each`
-->

## `[]`と`@each`の違い

<!--
Sometimes you don't care if properties of individual array items change. In this
case use the `[]` key instead of `@each`. Computed properties dependent on an array
using the `[]` key will only update if items are added to or removed from the array,
or if the array property is set to a different array. For example:
-->

配列内の各オブジェクトのプロパティの変更を気にしない場合もあります。
この場合、`@each`の代わりに`[]`キーを使用してください。
`[]`キーを使用して配列に依存する算出プロパティは、オブジェクトが配列に追加または配列から削除された場合、または配列が別の配列に変更された場合にのみ更新されます。
例えば、

```app/components/todo-list.js
import EmberObject, { computed } from '@ember/object';
import Component from '@ember/component';

export default Component.extend({
  todos: null,

  init() {
    this.set('todos', [
      EmberObject.create({ isDone: true }),
      EmberObject.create({ isDone: false }),
      EmberObject.create({ isDone: true }),
    ]);
  },

  selectedTodo: null,
  indexOfSelectedTodo: computed('selectedTodo', 'todos.[]', function() {
    return this.get('todos').indexOf(this.get('selectedTodo'));
  })
});
```

<!--
Here, `indexOfSelectedTodo` depends on `todos.[]`, so it will update if we add an item
to `todos`, but won't update if the value of `isDone` on a `todo` changes.
-->

上記のコードでは、`indexOfSelectedTodo`は`todos.[]`に依存するので、`todos`にオブジェクトを追加すると更新されますが、todoの`isDone`の値が変更された場合は更新されません。

<!--
Several of the [Ember.computed](https://www.emberjs.com/api/ember/release/classes/@ember%2Fobject%2Fcomputed) macros
utilize the `[]` key to implement common use-cases. For instance, to
create a computed property that mapped properties from an array, you could use
[Ember.computed.map](https://www.emberjs.com/api/ember/release/classes/@ember%2Fobject%2Fcomputed/methods/map?anchor=map)
or build the computed property yourself:
-->


[Ember.computed](https://www.emberjs.com/api/ember/release/classes/@ember%2Fobject%2Fcomputed)マクロの中には、共通のユースケースを実装するために`[]`キーを使用しているものがあります。
たとえば、配列からプロパティをマップした算出プロパティを作成するには、[Ember.computed.map](https://www.emberjs.com/api/ember/release/classes/@ember%2Fobject%2Fcomputed/methods/map?anchor=map)を使用するか、算出プロパティを自分で実装します。


<!--
```javascript
import EmberObject, { computed } from '@ember/object';

const Hamster = EmberObject.extend({
  excitingChores: computed('chores.[]', function() {
    return this.get('chores').map(function(chore, index) {
      return `CHORE ${index}: ${chore.toUpperCase()}!`;
    });
  })
});

const hamster = Hamster.create({
  chores: ['clean', 'write more unit tests']
});

hamster.get('excitingChores'); // ['CHORE 1: CLEAN!', 'CHORE 2: WRITE MORE UNIT TESTS!']
hamster.get('chores').pushObject('review code');
hamster.get('excitingChores'); // ['CHORE 1: CLEAN!', 'CHORE 2: WRITE MORE UNIT TESTS!', 'CHORE 3: REVIEW CODE!']
```
-->

```javascript
import EmberObject, { computed } from '@ember/object';

const Hamster = EmberObject.extend({
  // 訳注: choreは普段から行う雑用のことです
  excitingChores: computed('chores.[]', function() {
    return this.get('chores').map(function(chore, index) {
      return `CHORE ${index}: ${chore.toUpperCase()}!`;
    });
  })
});

const hamster = Hamster.create({
  chores: ['clean', 'write more unit tests']
});

hamster.get('excitingChores'); // ['CHORE 1: CLEAN!', 'CHORE 2: WRITE MORE UNIT TESTS!']
hamster.get('chores').pushObject('review code');
hamster.get('excitingChores'); // ['CHORE 1: CLEAN!', 'CHORE 2: WRITE MORE UNIT TESTS!', 'CHORE 3: REVIEW CODE!']
```

<!--
By comparison, using the computed macro abstracts some of this away:
-->

比較として、以下は算出プロパティマクロを使用した例です。上記のコードの一部を抽象化できます。

```javascript
import EmberObject from '@ember/object';
import { map } from '@ember/object/computed';

const Hamster = EmberObject.extend({
  excitingChores: map('chores', function(chore, index) {
    return `CHORE ${index}: ${chore.toUpperCase()}!`;
  })
});
```

<!--
The computed macros expect you to use an array, so there is no need to use the
`[]` key in these cases. However, building your own custom computed property
requires you to tell Ember.js that it is watching for array changes, which is
where the `[]` key comes in handy.
-->

上記の算出プロパティマクロは配列を使うことを前提としているので、これらの場合は`[]`キーを使う必要はありません。しかし、独自のカスタム算出プロパティを実装するには、Ember.jsに配列の変更を監視していることを伝える必要があります。その際、`[]`キーを使うと便利です。
