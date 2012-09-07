# Ember.js Docs 日本語訳

## イントロダクション

### Ember.jsとは

Ember.jsは大規模ウェブアプリケーションの構築に標準的なアプリケーション構造を提供し、かつボイラープレートコードを排除したJavaScriptのフレームワーク。

#### ボイラープレートコードの排除

全てのウェブアプリケーションには、例えば、データをサーバから取得し、スクリーンに表示させ、データに変更がある場合に表示をアップデートする、など、共通のタスクが存在する。

ブラウザ側が用意したツールは非常に原始的なため、これらタスクを行うのに同じコードを何回も書くことになる。Ember.jsはその数百回も書いたことがあるような同じコードを書くかわりにアプリそのものに集中できるツールを提供している。

いくつものアプリケーションを作成してきた経験から、明らかな低レベルのイベントドリブンの抽象化はもちろん、アプリケーション全体、特にDOMそのものにイベントを伝播させるのに必要なボイラープレートコードを排除している。

シンプルな例として`Person`のテンプレートを見て欲しい。

	{{person.name}} is {{person.age}}.

どんなテンプレートシステムでもテンプレートが初期にレンダリングされると現時点の`Person`の状態を反映させる。ボイラープレートコードを避けるためにEmber.jsでは`Person`の`name`あるいは`age`のデータを変更するとともにDOMも自動でアップデートされる。

テンプレートを1度指定すればEmber.jsはそのテンプレートが常にアップデートされるようにしている。

#### アプリケーション構造の提供

ウェブアプリケーションが静的なドキュメントでしかないウェブページから進化してきたためブラウザは十分な土台を用意していない。

Ember.jsはアプリケーションをModel、View、Controllerに簡単に分割することができる。このパターンはテストのしやすさを向上し、コードのモジュラー性を高め、プロジェクトに参加し始めたばかりのデベロッパがそのプロジェクトでどのように全体のJavaScriptソースが組み上がっているかの理解をサポートする。コールバックによるスパゲッティコードの時代は終わったのだ。

### Ember.jsの特徴

従来のウェブアプリケーションではユーザに対しサーバとやり取りをするたびに常に新しいページをダウンロードさせる。つまりすべてのやり取りにおいてアプリケーション(のサーバ)とユーザ間にある遅延時間以上のスピードを出すことはできない。AJAXを活用してページの中のあるパーツのみを差し替えるという方法はある程度はプラスになるがUIのアップデートを行うためにはサーバとの接続のラウンドトリップは避けることができない。そしてページ内の複数パーツを同時にアップデートを行う必要があるケースではほとんどのデベロッパは全ての要素を同期するのは扱いづらいのでページの再読み込みに頼ることになる。

Ember.jsは、ほかのモダンJavaScriptフレームワークと同じく、若干異なる実装を行っている。多くのアプリケーションのロジックをサーバにおくのではなく、Ember.jsアプリケーションは動作するために必要なすべてを初期ページロードの際にダウンロードする。つまりユーザがアプリを利用している場合、ユーザは新しいページをロードする必要もなくなり、ユーザのインタラクションによるUIの反応速度は非常に早い。

この構造の利点の1つはサーバサイドに大きく依存するアプリや第三者が利用するREST APIを扱うことができることだ。こうすることでバックエンドデベロッパは安全で信頼出来る早いAPIサーバの構築にフォーカスでき、フロントエンドデベロッパがエキスパートである必要もない。

### Ember.jsを簡単に紹介

Emberを楽しく利用できる3つの特徴が以下:

- バインディング
- 算出済みプロパティ(Computed properties)
- 自動でアップデートするテンプレート

#### バインディング

バインディングを使って2つの異なるオブジェクト内のプロパティの同期を取ることができる。バインディングを1度定義すればEmberが変更を双方向に伝播させる。

以下が2つのオブジェクト間でバインディングを生成する方法となる:

	MyApp.president = Ember.Object.create({
		name: "Barack Obama"
	});
	MyApp.country = Ember.Object.create({
		// 'Binding'という文字列をプロパティ名の最後に付与すると
		// Emberは`presidentName`プロパティに対してバインディングを行う
		presidentNameBinding: 'MyApp.president.name'
	});
	// その後、Emberがバインディングを解消した後…
	MyApp.country.get('presidentName');
	// 結果: "Barack Obama"

バインディングはアプリケーションをMVC(Model-View-Controller)パターンを使って設計することを可能にし、データが常にレイヤ間で正しく循環する安心を得ることができる。

#### 算出済みプロパティ (Computed properties)

算出済みプロパティ(Computed properties)は関数をプロパティのように扱うことができる:

	MyApp.president = Ember.Object.create({
		firstName: "Barack",
		lastName: "Obama",
		fullName: function() {
		return this.get('firstName') + ' ' + this.get('lastName');
		// 以下の様にして関数をプロパティとして扱うことができる
		}.property()
	});
	MyApp.president.get('fullName');
	// 結果: "Barack Obama"

算出済みプロパティ(Computed properties)が有用な理由はほかのプロパティと同じくバインディングを利用できる点だ。

算出済みプロパティ(Computed properties)はほかのプロパティに依存することが多い、例えば上の例だと`fullName`プロパティは`firstName`と`lastName`によって値を確定することができる。Emberではこのような依存を以下のように設定できる:

	MyApp.president = Ember.Object.create({
		firstName: "Barack",
		lastName: "Obama",
		fullName: function() {
    			return this.get('firstName') + ' ' + this.get('lastName');
			// Emberに対してこの算出済みプロパティ(Computed properties)が
			// firstNameとlastNameに依存関係がある設定を行う
		}.property('firstName', 'lastName')
	});

算出済みプロパティ(Computed properties)に関連するバインディングをアップデートする際にEmberがこれらの依存についても解決するように依存関係の設定をきちんとしておくこと。

#### 自動でアップデートするテンプレート

EmberはHandlebar、セマンティックなテンプレートライブラリを利用している。JavaScriptアプリケーションでデータを取得し、DOMに挿入するには`<script>`タグをHTML内の表示するべき場所に作成すればいい:

	<script type="text/x-handlebars">
		The President of the United States is {{MyApp.president.fullName}}.
	</script>

テンプレートもまたバインディングの対象になっている点が最も優れている点だ。どういう意味かというと、表示させるプロパティの値を変更したら、自動で表示をアップデートするということになる。そして依存関係を明確にしてれば、依存しているプロパティの値を変更しても表示のアップデートが行われる。

これら3つの強力なツールがどの様に動作するかが分かっただろうか？ まずは簡単なプロパティからはじめてそれからより洗練されたプロパティを設定し、算出済みプロパティ(Computed properties)を使って依存関係を明確にしていく。データがどのようなものであるかが分かりさえすれば、どのように表示するかを設定するのは1度だけで残りはEmberが解決できる。データをXHRのリクエストから取得しても、あるいはユーザアクションによるものでも、UIは常に最新の状態を保つことができる。このことはデベロッパが毎日頭を抱える様々な種類のエッジケースを排除できる。

## 早速使ってみよう

ニーズによっていくつか初めてのEmber.jsアプリ作成方法がある。

もしニーズがシンプルなものかあるいはただEmber.jsを使ってみたいだけであれば、Ember.jsのスタータキットをダウンロードしてほしい。
スタータキットは[HTML5 Boilerplate](http://html5boilerplate.com/)をベースにしている。ビルドツールやそのほかのツールは必要ない。スタータキットをダウンロードし解凍するだけでいい。Handlebarsのテンプレートを編集する場合は`index.html`にて行い、Ember.jsアプリそのものは`javascripts/app.js`に格納してある。

より大規模なアプリケーションの場合にはRuby on Railsの利用を考慮してほしい。Railsはソースコードやその他のアセットを管理する手助けをしてくれるだけではなく、REST APIも供給している。

## Ember オブジェクトモデル

Emberは単純なJavaScriptのオブジェクトモデルを拡張し、バインディングとオブザーバを追加する。それだけではなく、より強力なミックスインを利用したアプローチでのコード共有もサポートする。

最も単純な例として`Ember.Object`で`extend`メソッドを使ってEmberクラスを作成することができる。

	Person = Ember.Object.extend({
		say: function(thing) {
		alert(thing);
		}
	});

新しいクラスを作成したら、`create`メソッドを使って新しいインスタンスを生成できる。クラス内で設定したプロパティ全てがインスタンスでも利用できる。

	var person = Person.create();
	person.say("Hello") // alerts "Hello"

インスタンスを生成する際、プロパティをオブジェクトに渡すことで追加することができる。

	var tom = Person.create({
		name: "Tom Dale",
		helloWorld: function() {
		this.say("Hi my name is " + this.get('name'));
		}
	});
	tom.helloWorld() // alerts "Hi my name is Tom Dale"

Emberはバインディングとオブザーバをサポートしているため、`get`メソッドを使っていつでもプロパティを取得でき、`set`メソッドを使ってプロパティの設定もできる。

オブジェクトに対して新しいインスタンスを生成する際、クラスで設定したメソッド、あるいはプロパティを上書きすることもできる。例えば以下の例では`Person`クラスの`say`メソッドを上書きしている。

	var yehuda = Person.create({
		name: "Yehuda Katz",
		say: function(thing) {
		var name = this.get('name');
			this._super(name + " says: " + thing);
		}
	});

オブジェクト内で`_super`メソッド(`super`はJavaScriptの予約語)を使って上書きした元のメソッドを呼び出すこともできる。

### クラス、サブクラス

クラス内にサブクラスを作成するのにも`extend`メソッドを使うことができる。それどころか上の例でも、`extend`メソッドを`Ember.Object`で利用した際にも実は`Ember.Object`のサブクラスを作成していたことになる。

	var LoudPerson = Person.extend({
		say: function(thing) {
			this._super(thing.toUpperCase());
		}
	});

サブクラスを生成した場合に`this._super`を使って上書きしたメソッドを実行することができる。

### クラスとインスタンスの再開

クラスは一度に全て定義する必要はなく、`reopen`メソッドを使っていつでもクラスを再開し、新しいプロパティを設定することができる。

	Person.reopen({
		isPerson: true
	});
	Person.create().get('isPerson') // true

`reopen`メソッドでは`this._super`を利用することもできるし、既存のメソッドを上書きすることもできる。

	Person.reopen({
		// `say`メソッドを ! を最後に追加し上書きする
		say: function(thing) {
			this._super(thing + "!");
		}
	});

例にあるように、`reopen`はインスタンスに対してメソッドやプロパティを追加するのに利用している。クラスメソッドを作成したい場合、あるいはクラスそのものにプロパティを追加する場合は`reopenClass`を利用することができる。

	Person.reopenClass({
		createMan: function() {
			return Person.create({isMan: true})
		}
	});
	Person.createMan().get('isMan') // true

### 算出済みプロパティGetters

多くの場合、あるプロパティをほかのプロパティの実行結果を元に生成したいケースがある。Emberのオブジェクトモデルでは通常のクラス設定内で算出済みプロパティを簡単に設定することができる。

	Person = Ember.Object.extend({
		// these will be supplied by `create`
		firstName: null,
		lastName: null,
		fullName: function() {
			var firstName = this.get('firstName');
			var lastName = this.get('lastName');
			return firstName + ' ' + lastName;
		}.property('firstName', 'lastName')
	});
	var tom = Person.create({
		firstName: "Tom",
		lastName: "Dale"
	});
	tom.get('fullName') // "Tom Dale"

Emberのプロトタイプ拡張を利用していない場合でも関数を`Ember.computed`で囲む若干冗長な方法でも可能だ。

	Person = Ember.Object.extend({
		// these will be supplied by `create`
		firstName: null,
		lastName: null,
		fullName: Ember.computed(function() {
			var firstName = this.get('firstName');
			var lastName = this.get('lastName');
			return firstName + ' ' + lastName;
		}).property('firstName', 'lastName')
	});

`property`メソッドは関数を算出済みプロパティ(Computed properties)として定義するだけではなく、依存関係も定義する。  
これらの依存関係はこの後バインディングとオブザーバの説明をする際に再度出てくる。

クラス内にサブクラスを設定する場合、または新しいインスタンスを生成する場合に、すべての算出済みプロパティ(Computed properties)は上書きすることができる。

### 算出済みプロパティSetters

算出済みプロパティ(Computed properties)をセットする際にEmberが何をするべきかも定義することができる。算出済みプロパティ(Computed properties)をセットする際に設定したいキーと値とともに実行することが可能だ。

	Person = Ember.Object.extend({
		// these will be supplied by `create`
		firstName: null,
		lastName: null,
		fullName: Ember.computed(function(key, value) {
			// getter
			if (arguments.length === 1) {
				var firstName = this.get('firstName');
				var lastName = this.get('lastName');
				return firstName + ' ' + lastName;
				// setter
			} else {
				var name = value.split(" ");
				this.set('firstName', name[0]);
				this.set('lastName', name[1]);
				return value;
			}
		}).property('firstName', 'lastName')
	});
	var person = Person.create();
	person.set('fullName', "Peter Wagenet");
	person.get('firstName') // Peter
	person.get('lastName') // Wagenet

Emberは算出済みプロパティ(Computed properties)をセッターでもゲッターでも呼び出すことができ、引数の数によってセッターとして呼び出されたか、ゲッターとして呼び出されたかを確定できる。

### オブザーバー

Emberではどのプロパティでも、もちろん算出済みプロパティ(Computed properties)も、オブザーバを設定することができる。`addObserver`メソッドを使ってオブザーバをセットする。

	Person = Ember.Object.extend({
		// these will be supplied by `create`
		firstName: null,
		lastName: null,
		fullName: Ember.computed(function() {
			var firstName = this.get('firstName');
			var lastName = this.get('lastName');
			return firstName + ' ' + lastName;
		}).property('firstName', 'lastName')
	});
	var person = Person.create({
		firstName: "Yehuda",
		lastName: "Katz"
	});
	person.addObserver('fullName', function() {
		// deal with the change
	});
	person.set('firstName', "Brohuda"); // observer will fire

算出済みプロパティ(Computed properties)である`fullName`は`firstName`に依存しているため、`firstName`がアップデートされれば、`fullName`のオブザーバも実行される。

オブザーバは非常に一般的であるため、Emberではクラス内でオブザーバを設定する方法も提供している。

	Person.reopen({
		fullNameChanged: function() {
			// .addObserverのインラインバージョン
		}.observes('fullName')
	});

プロトタイプ拡張を利用していない場合でも`Ember.observer`メソッドを使ってインラインでのオブザーバの設定が可能になる。

	Person.reopen({
		fullNameChanged: Ember.observer(function() {
		//  .addObserverのインラインバージョン
		}, 'fullName')
	});

#### 配列内の変更

算出済みプロパティ(Computed properties)を配列内の全ての要素に依存する形で設定することも多い。例えばコントローラー内にあるすべてのTODOアイテム数を数え、その内いくつが完了のステータスを持っているかを算出したい場合などがそれにあたる。

以下がその場合の算出済みプロパティ(Computed properties)の例:

	App.todosController = Ember.Object.create({
		todos: [
		Ember.Object.create({
			isDone: false
		})],
		remaining: function() {
			var todos = this.get('todos');
			return todos.filterProperty('isDone', false).get('length');
		}.property('todos.@each.isDone')
	});

`@each`という特別なキーが依存関係内(`todos.@each.isDone`)にあることに注目してほしい。こうすることでEmber.jsに対して以下の4つのイベントでバインディングのアップデートとこの算出済みプロパティ(Computed properties)でのオブザーバの実行を行うように指示することができる。

- `todos`配列内のオブジェクトでの`isDone`プロパティの変更
- `todos`配列へのアイテムの追加
- `todos`配列でのアイテムの削除
- コントローラー内の`todos`プロパティが別の配列に変更

上記の例では`remianing`は`1`になる:

	App.todosController.get('remaining');
	// 1

todo内の`isDone`プロパティを変更すると、`remianing`プロパティは自動でアップデートされる:

	var todos = App.todosController.get('todos');
	var todo = todos.objectAt(1);
	todo.set('isDone', true);
	App.todosController.get('remaining');
	// 0
	todo = Ember.Object.create({ isDone: false });
	todos.pushObject(todo);
	App.todosController.get('remaining');
	// 1

### バインディング

バインディングは2つのプロパティ間にリンクを形成する。どちらか片方が新しい値でアップデートされればもう片方も自動的にアップデートされる。バインディングは同じオブジェクト内のプロパティをリンクすることもできるし、異なるオブジェクト間でもリンクできる。バインディングを実装しているほかのフレームワークとは異なり、Ember.jsのバインディングはviewとmodel間のオブジェクトだけではなくどのオブジェクトでも利用できる。

2-wayバインディングを作成するもっとも手軽な方法は新しいプロパティに`Binding`という文字列を最後に追加し、グローバルスコープからのパスを指定する方法だ:

	App.wife = Ember.Object.create({
		householdIncome: 80000
	});
	App.husband = Ember.Object.create({
		householdIncomeBinding: 'App.wife.householdIncome'
	});
	App.husband.get('householdIncome'); // 80000
	// Someone gets raise.
	App.husband.set('householdIncome', 90000);
	App.wife.get('householdIncome'); // 90000

バインディングはすぐにアップデートしないことに注意してほしい。Emberではアプリケーションのコードがすべて実行されるまで同期を待つ。こうすることでバインドされたプロパティを何度変更しても値が一過性であっても同期にかかる無駄を排除する心配がなくなる。

#### 1-wayバインディング

1-wayバインディングは変更を1方向にのみ伝播させる。通常は1方向バインディングはパフォーマンス最適化に利用され、2-wayバインディングのシンタックスをより簡潔にすることができる。

	App.user = Ember.Object.create({
		fullName: "Kara Gates"
	});
	App.userView = Ember.View.create({
		userNameBinding: Ember.Binding.oneWay('App.user.fullName')
	});
	// userオブジェクト内のnameの変更は
	// view内の値を変更する。
	App.user.set('fullName', "Krang Gates");
	// App.userView.userNameは"Krang Gates"に変更
	// …しかしViewへの変更はオブジェクトには反映されない
	App.userView.set('userName', "Truckasaurus Gates");
	App.user.get('fullName'); // "Krang Gates"

### 何をいつ使えばいいのか？

慣れないうちは算出済みプロパティ(Computed properties)やバインディング、そしてオブザーバをいつ使っていいのか分からない場合もある。その場合は以下のガイドラインを参照してほしい:

− 算出済みプロパティ(Computed properties)はほかのプロパティを使って新しいプロパティを合成する際に利用する。算出済みプロパティ(Computed properties)はアプリケーションの動作を含むべきではなく、通常は呼び出された際に副作用を引き起こすべきではない。ただし、稀なケースとして、複数回に及ぶ同じ算出済みプロパティ(Computed properties)の呼び出しは常に同じ値を返すべきだ(もちろん依存しているプロパティに変更がなければ)

- オブザーバはほかのプロパティの変更に反応する動作を定義するべき。オブザーバはとくにバインディングの同期が終わった際に何かの動作を行う際に非常に有効。

- バインディングはあるオブジェクトが2つの異なるレイヤーが常に同期を取る状態をであることを確実にするためによく利用される。例えば、コントローラーに対してHandlebarsを使ってViewをバインドするなど。もちろん2つのオブジェクトを同じレイヤー内で同期を取るのにも利用できる。例えば`App.selectedContactController`が`App.contactsController`の`selectedContact`にバインドするなどという場合がそう。

## ネームスペースの作成

全てのEmberアプリは`Ember.Application`インスタンスを持たなければならない。このオブジェクトはグローバルでアクセスできるネームスペースとしての役割を果たし、アプリ内のほかのクラスやインスタンスからアクセスすることができる。さらにページに対してイベントリスナを設定し、UIに対するユーザのアクションのイベントをviewで受け取ることができる(詳しくはこの後で)

以下がアプリケーションの例:

	window.App = Ember.Application.create();

ネームスペースはどんな名前でも問題ないがバインディングが認識するために大文字で開始されなければならない。

もしEmberアプリケーションを現存するサイトにエンベットする際には特定の要素に対して`rootElement`プロパティを使ってイベントリスナを設定することができる:

	window.App = Ember.Application.create({
		rootElement: '#sidebar'
	});

## UIをHandlebarsを使って表現する

### Handolebars

Emberにはセマンティックテンプレート言語である[Handlebars](http://handlebarsjs.com/)がバンドルされている。このテンプレートは通常のHTMLに式が埋め込まれたような見た目になる。

Handlebarsのテンプレートはアプリケーション内のHTMLファイルに格納するべきで、実行時にEmberはテンプレートをコンパイルしview内で利用できるようにする。

`<body>`タグ内に`<script>`タグを挿入することでドキュメントに対してテンプレートを導入することができる:

		<html>
		<body>
			<script type="text/x-handlebars">
				Hello, <b>{{MyApp.name}}</b>
			</script>
		</body>
		</html>

後で利用するテンプレートを設定するには`<script>`タグに`data-template-name`属性を追加する:

	<html>
		<head>
			<script type="text/x-handlebars" data-template-name="say-hello">
			Hello, <b>{{MyApp.name}}</b>
			</script>
		</head>
	</html>

### Ember.View

`Ember.View`を利用してHandlebarsのテンプレートをDOMに挿入することも出来る。

viewに対してどのテンプレートを利用するかを指定するためにには`templateName`プロパティに指定すればいい。もし`<script>`タグが以下だったとする:

	<html>
		<head>
			<script type="text/x-handlebars" data-template-name="say-hello">
				Hello, <b>{{name}}</b>
			</script>
		</head>
	</html>

`templateName`プロパティを`"say-hello"`と設定する。

	var view = Ember.View.create({
		templateName: 'say-hello',
		name: "Bob"
	});

注意: 本ガイドでは以下`templateName`プロパティはほとんどの例で省略している。`Ember.View`とHandlebarsのテンプレートを使ったコードサンプル内ではviewは`templateName`プロパティを使ってテンプレートを指定して表示されているとしてほしい。

`appendTo`を使ってviewをドキュメントに付与できる:

	  view.appendTo('#container');

短縮系として`append`を使ってドキュメントボディに追加できる:

	view.append();

ドキュメントからviewを削除するには`remove`を利用する:

	view.remove();

### Handlebars: 基本

これまで見てきたように、Handlebarsの式内あるいは中カッコ内(`{{}}`)にプロパティを指定することでその値を表示できる:

	My new car is {{color}}.

上の例ではView内の`color`プロパティを検索し表示する。例えばviewが以下だったとする:

	App.CarView = Ember.View.extend({
		color: 'blue'
	});

viewはブラウザ上で以下のように表示される:

	My new car is blue.

またグローバスパスを指定することもできる:

	My new car is {{App.carController.color}}.

(Emberではパスがグローバルであるか、Viewに対してリラティブであるかを始めの文字が大文字かどうかで判定する。このため`Ember.Application`インスタンスが大文字で始まっている必要がある。)

本ガイド内で説明している特徴はすべてバインディングすることができる。つまりもし、テンプレート内で使われている値が変更されたら、HTMLも魔法のように自動的に更新される。

内在するプロパティの変更によりHTML内のどの部分がアップデートされるかを知るためには、HandlebarsはユニークなIDを使ってマーカ要素を追加する。アプリケーションの実行中に確認すると以下のような追加要素に気がつくだろう:

	My new car is
	<script id="metamorph-0-start" type="text/x-placeholder"></script>
	blue
	<script id="metamorph-0-end" type="text/x-placeholder"></script>.

Handlebarsの式がこのマーカに囲まれているので、HTMlタグは同じブロック内にある必要がある。例えば以下のようにしてはいけない:

	<!-- Don't do it! -->
	<div {{#if }}class="urgent"{{/if}}>

もしプロパティの出力をマーカで囲みたくない場合は`unbind`ヘルパを利用する:

	My new car is {{unbound color}}.

出力からマーカは削除されるが自動アップデートはされないので注意が必要だ。

	My new car is blue.

### {{#if}}、{{else}} と {{#unless}}

プロパティが存在する場合にのみテンプレート内にある特定の部分を表示したいケースがある。例えば、view内に`firstName`と`lastName`オブジェクトを含む`person`プロパティがあるとして:

	App.SayHelloView = Ember.View.extend({
		person: Ember.Object.create({
			firstName: "Joy",
			lastName: "Clojure"
		})
	});

`person`オブジェクトが存在する場合のみにテンプレート内のある部分を表示させるためには`{{#if}}`ヘルパを使ってブロックのレンダリングに条件付けすることができる:

	{{#if person}}
		Welcome back, <b>{{person.firstName}} {{person.lastName}}</b>!
	{{/if}}

Handlebarsは引数が`false`、`undefined`、`null`あるいは`[]`(どのfalsy値でも)と判定される場合にこのブロックをレンダリングしない。

`{{else}}`を利用して式がfalseになる場合に別テンプレートを使い表示を行える:

	{{#if person}}
		Welcome back, <b>{{person.firstName}} {{person.lastName}}</b>!
	{{else}}
		Please log in.
	{{/if}}

`{{#unless}}`を利用して値がfalseの場合にのみ表示するブロックを指定できる;

	{{#unless hasPaid}}
		You owe: ${{total}}
	{{/unless}}

`{{#if}}`と`{{#unless}}`はブロック式の例。これらはテンプレート内の一部でヘルパを実行することを可能にする。ブロック式は通常の式と同じような見た目だが、ヘルパ名の前にハッシュ(#)を含み、式を閉じる必要がある。

### {{#with}}

テンプレート内のセクションを`Ember.View`ではないコンテキストで実行したい場面がある。たとえば、`{{#with}}`ヘルパを使って上の例のテンプレートを整理することができる:

	{{#with person}}
		Welcome back, <b>{{firstName}} {{lastName}}</b>!
	{{/with}}

`{{#with}}`ではブロックのコンテキストを変更できる。コンテキストとはどのオブジェクトのプロパティを探すかの指定。デフォルトではコンテキストはテンプレートが属する`Ember.View`となる。

### {{bindAttr}} を使って要素の属性をバインドする

テキスト以外にもテンプレート内のHTML要素の属性を指定するような場合がある。たとえばURLを含むviewを想定して欲しい:

	App.LogoView = Ember.View.extend({
		logoUrl: 'http://www.mycorp.com/images/logo.png'
	});

Handlebars内でURLを画像として表示する最適な方法は以下:

	<div id="logo">
		<img {{bindAttr src ="logoUrl"}} alt="Logo">
	</div>

そしてこれは以下のHTMLを生成する:

	<div id="logo">
		<img src="http://www.mycorp.com/images/logo.png" alt="Logo">
	</div>

`{{bindAttr}}`をブーリアン値として利用する場合、属性を追加、あるいは削除する。例えば以下のEmber viewでは:

	App.InputView = Ember.View.extend({
		isDisabled: true
	});

そして以下のテンプレート:

	<input type="checkbox" {{bindAttr ="isDisabled"}}>

Handlebarsは以下のHTMLを生成する:

	<input type="checkbox" disabled>

### {{bindAttr}}を使ってクラス名をバインドする

`class`属性はほかの属性と同じようにバインドすることができるが、特別な動作も追加される。デフォルトの動作は期待通り:

JS:

	App.AlertView = Ember.View.extend({
		priority: "p4",
		isUrgent: true
	});

HTML: 

	<div {{bindAttr ="priority"}}>
		Warning!
	</div>

このテンプレートは以下のHTMLを出力する:

	<div class="p4">
		Warning!
	</div>

バインドする値がブーリアン値の場合、ダッシュが追加されたプロパティがクラスとして扱われる:

	<div {{bindAttr ="isUrgent"}}>
		Warning!
	</div>

このテンプレートは以下のHTMLを出力する:

	<div class="is-urgent">
		Warning!
	</div>

ほかの属性値と異なり、複数クラスをバインドすることもできる:

	<div {{bindAttr ="isUrgent "}}>
		Warning!
	</div>

ダッシュ追加方式のクラス名のほかに自由に名前を指定することもできる:

	<div {{bindAttr ="isUrgent:urgent"}}>
		Warning!
	</div>

このケースでは`isUrgent`プロパティが正になる場合、`urgent`クラスが追加され、もし偽になる場合は`urgent`が削除される。

### {{action}}を使ってイベントを扱う

`{{action}}`ヘルパを使って要素上で発生させるイベントをviewクラスに付随させる:

`click`イベントを`edit()`に付与する:

	<a href="#" {{action "edit" on="click"}}>Edit</a>

`click`イベントはデフォルトイベントのため、以下のようにより短く記述することもできる:

	<a href="#" {{action "edit"}}>Edit</a>

`{{action}}`ヘルパを含むviewをデフォルトでターゲットにするが、別のviewをターゲットにすることもできる:

	<a href="#" {{action "edit" target="parentView"}}>Edit</a>

アクションハンドラはjQueryのイベントオブジェクトを受け入れることもでき、`view`と`context`プロパティを拡張する。これらのプロパティは別のviewをターゲットにする場合に有用になる。例:

	App.ListingView = Ember.View.extend({
		templateName: 'listing',
		edit: function(event) {
			event.view.set('isEditing', true);
		}
	});

上にあるテンプレートはどれも以下のHTMLを生成する:

	<a href="#" data-ember-action="3">Edit</a>

Emberは内部的に設定された`data-ember-action`idを通してターゲットviewのイベントを委譲する。

### Viewの階層構造を作る

これまでは1つのビューに対してテンプレートを書く方法を見てきた。しかしアプリケーション開発を進めていくうちにページ内の異なるエリアにviewの階層構造をカプセル化する必要に迫られる場合がある。それぞれのviewが表示するプロパティのメンテナンスそしてイベントの管理を担当する必要がある。

### {{view}}

`{{view}}`ヘルパを使って親に対して子になるviewを追加できる。`{{view}}`ヘルパにはviewクラスのパスを指定する。

JS: 

	// Define parent view
	App.UserView = Ember.View.extend({
		templateName: 'user',
		firstName: "Albert",
		lastName: "Hofmann"
	});
	// Define child view
	App.InfoView = Ember.View.extend({
		templateName: 'info',
		posts: 25,
		hobbies: "Riding bicycles"
	});

HTML:

	User: {{firstName}} {{lastName}}
	{{view App.InfoView}}

HTML:

	<b>Posts:</b> {{posts}}
	<br>
	<b>Hobbies:</b> {{hobbies}}

もし`App.UserView`のインスタンスを生成し、レンダリングしたとすると、DOMでは以下のように扱われる:

	User: Albert Hofmann
	<div>
		<b>Posts:</b> 25
		<br>
		<b>Hobbies:</b> Riding bicycles
	</div>

#### リラティブパス

アブソルートパスの代わりに、どのviewクラスを利用するかを親になるviewからのリラティブパスで指定することもできる。
例えば上記のviewの階層構造を以下のようにすることも可能:

JS: 

	App.UserView = Ember.View.extend({
		templateName: 'user',
		firstName: "Albert",
		lastName: "Hofmann",
		infoView: Ember.View.extend({
			templateName: 'info',
			posts: 25,
			hobbies: "Riding bicycles"
		})
	});

HTML:

	User: {{firstName}} {{lastName}}
	{{view infoView}}

Emberは大文字で始まるプロパティをグローバルとして扱うため、viewクラスをこのように入れ子状態にする場合、小文字を利用すること。

### 子になるviewテンプレートの設定

メインのテンプレート内で子になるViewをインラインで指定したい場合にはブロック形式の`{{view}}`ヘルパを使うことが出来る。上記の例であれば以下のように書き換えられる:

JS:

	App.UserView = Ember.View.extend({
		templateName: 'user',
		firstName: "Albert",
		lastName: "Hofmann"
	});
	App.InfoView = Ember.View.extend({
		posts: 25,
		hobbies: "Riding bicycles"
	});

HTML:

	User: {{firstName}} {{lastName}}
	{{#view App.InfoView}}
		<b>Posts:</b> {{posts}}
		<br>
		<b>Hobbies:</b> {{hobbies}}
	{{/view}}

このように処理を行う場合にはページの1部分に対してviewを割り当てている、というように考えるとよい。こうすることでイベントハンドリングをそのページ内の1部分にカプセル化することも可能。

### バインディングの設定

ここまでの例ではviewに対して静的な値を設定してきたが、MVC構造を実装する上で最もよい方法はViewのプロパティをControllerレイヤーにバインディングする方法だ。

ではControllerをユーザデータを表現するように設定してみよう:

	App.userController = Ember.Object.create({
		content: Ember.Object.create({
			firstName: "Albert",
			lastName: "Hofmann",
			posts: 25,
			hobbies: "Riding bicycles"
		})
	});

`App.UserView`をアップデートし`App.userController`にバインディングしてみよう:

	App.UserView = Ember.View.extend({
		templateName: 'user',
		firstNameBinding: 'App.userController.content.firstName',
		lastNameBinding: 'App.userController.content.lastName'
	});

`App.UserView`の例のように少ないバインディングを設定する場合はテンプレート内でこれらのバインディングを宣言するほうが楽な場合もある。`{{#view}}`ヘルパに追加の引数を渡すことでそうすることも可能。もしバインディングの設定しかしていない場合はこの機能を使うことで新しいサブクラスを作ることを回避することができる。

	User: {{firstName}} {{lastName}}
	{{#view App.UserView postsBinding="App.userController.content.posts"
		hobbiesBinding="App.userController.content.hobbies"}}
		<b>Posts:</b> {{posts}}
		<br>
		<b>Hobbies:</b> {{hobbies}}
	{{/view}}

注意: `{{view}}`の引数としてバインディングのみだけではなくどんなプロパティを渡すこともできるが、バインディングの設定以外の実装が必要な場合はサブクラスを作成したほうが良策と言える。

### ViewのHTMLを変更する

viewを挿入する際、コンテンツを追加した状態のHTML要素が生成される。もしviewが子になるviewを持つ場合、親のHTML要素内に子ノードとして表示される。

デフォルトでは新しい`Ember.View`インスタンスは`<div>`要素を生成するが、`tagName`パラメータを使って上書きすることもできる:

	{{view App.InfoView tagName="span"}}

`id`パラメータを渡すことでHTML要素に対してID属性を指定することができる:

	{{view App.InfoView id="info-view"}}

こうすることでCSSのIDセレクタを使って簡単にスタイルを追加できる:

	/** Give the view a red background. **/
	#info-view {
		background-color: red;
	}

同様にクラス名を指定することもできる:

	{{view App.InfoView class="info urgent"}}

クラス名はviewのプロパティに対して`class`の代わりに`classBinding`を使ってバインドすることもできる。こうすることで`bindAttr`が持つ動作と同じ動作を適応することができる:

JS:

	App.AlertView = Ember.View.extend({
		priority: "p4",
		isUrgent: true
	});

HTML:

	{{view App.AlertView classBinding="isUrgent priority"}}

こうすることでviewラッパは以下のような結果になる:

	<div id="sc420" class="sc-view is-urgent p4"></div>

### リストアイテムを表示する

オブジェクトのリストを一覧表示する場合は、Handlebarsの`{{#each}}`ヘルパを利用する:

JS:

	App.PeopleView = Ember.View.extend({
		people: [ { name: 'Yehuda' },
		{ name: 'Tom' } ]
	});

HTML:

	<ul>
		{{#each people}}
		<li>Hello, {{name}}!</li>
		{{/each}}
	</ul>

以下のようリストを表示する:

	<ul>
		<li>Hello, Yehuda!</li>
		<li>Hello, Tom!</li>
	</ul>

リスト内の全てのアイテムに対してviewを作成する場合は現在のコンテキストに対してview内のプロパティをバインドすることもできる。
例えば、以下の例ではリスト内の全アイテムに対してviewを作成し、そのアイテムに対して`content`プロパティを設定する:

	{{#each App.peopleController}}
		{{#view App.PersonView contentBinding="this"}}
		{{content.firstName}} {{content.lastName}}
		{{/view}}
	{{/each}}

### カスタムヘルパの作成方法

アプリケーション内で同じHTMLを利用するケースは多いが、このような場合にHandlebarsのテンプレート内で実行できるカスタムヘルパを登録することができる。

例えば特定の値に対し頻繁に`<span>`タグにカスタムクラスを追加するようなケースの場合に以下のようにJavaScriptからヘルパを登録できる:

	Handlebars.registerHelper('highlight', function(property) {
		var value = Ember.getPath(this, property);
		return new Handlebars.SafeString('<span class="highlight">'+value+'</span>');
	});

HTMLをヘルパから戻す場合、そしてエスケープしたくない場合には`SafeString`を戻すようにすること。

Handlebarsテンプレート内でこのヘルパを実行できる:

	{{highlight name}}

そして以下のように出力される:

	<span class="highlight">Peter</span>

注意: ヘルパ関数に対して渡されるパラメータは現在の値としてだけではなくnameとして渡される。こうすることで値に対してオブザーバを設定することが可能になる。パラメータの現在の値を取得するには上の例のように`Ember.getPath`を利用すること。

### 設定済みのView

Emberはテキストインプットやチェックボックス、セレクトリストなどの基本的なコントロールのセットがパッケージされている。

以下のに例を示す:

#### Ember.Checkbox

	<label>
		{{view Ember.Checkbox checkedBinding="content.isDone"}}
		{{content.title}}
	</label>

#### Ember.TextField

	App.myText = Ember.TextField.extend({
        formBlurredBinding: 'App.adminController.formBlurred',
        change: function(evt) {
          this.set('formBlurred', true);
        }
      });

#### Ember.Select

    {{view Ember.Select viewName="select"
                          contentBinding="App.peopleController"
                          optionLabelPath="content.fullName"
                          optionValuePath="content.id"
                          prompt="Pick a person:"
                          selectionBinding="App.selectedPersonController.person"}

#### Ember.TextArea

    var textArea = Ember.TextArea.create({
            valueBinding: 'TestObject.value'

上記のコントロールをViewに追加したい場合は、上記から利用することを推奨している。

イベントはサブビューから親となるViewに対してバブルしないため、これらのViewを拡張することがそのようなイベントをキャプチャする唯一の方法になる。

例:

	App.myText = Ember.TextField.extend({
		formBlurredBinding: 'App.adminController.formBlurred',
		change: function(evt) {
			this.set('formBlurred', true);
		}
	});

こうすることでこのviewをサブviewとして利用できかつイベントをキャプチャできる。以下の例ではNameインプットへの変更がフォームへのフォーカスを奪い、保存ボタンを表示する。

	<script id="formDetail" data-template-name='formDetail' type="text/x-handlebars">
	<form>
	<fieldset>
		<legend>Info:</legend>
		{{view App.myText name="Name" id="Name" valueBinding="myObj.Name"}}
		<label for="Name">Name</label><br/>
			{{#if formBlurred}}
			<a href="#" {{action "syncData" on="click"}}>Save</a>
			{{/if}}
	</fieldset>
	</form>
	</script>

## Viewをより掘り下げる

Handlebarsの扱いに慣れてきたらイベントの扱いとニーズに応じたviewのカスタマイズ方法について見ていこう。

### イベントハンドラ

イベントリスナーを要素に対して登録する代わりに、view内のメソッドとしてイベント名を実装したいとする。

例えば以下のようなテンプレートがあったとして:

	{{#view App.ClickableView}}
		This is a clickable area!
	{{/view}}

` App.ClickableView`をクリックした場合、alertを表示させてみよう:

	App.ClickableView = Ember.View.extend({
		click: function(evt) {
		alert("ClickableView was clicked!");
		}
	});

イベントはターゲットviewからそれぞれの親になるviewに対してリードオンリーになる値がルート値に設定されていない限り連続してバブルアップしていく。もしJavaScriptを使って(handlebarsの`{{view}}`を使わずに作成したとして)Viewを管理する場合には以下の`Ember.ContainerView`に対するドキュメントを読んでほしい。

### `Ember.ContainerView`を使ってviewを手動で管理する

通常であればviewは子になるviewを`{{view}}`ヘルパを使って生成するが、手動でviewの子になるviewを管理するほうが便利な場合もある。
`Ember.ContainerView`のインスタンスを作成し、`childViews`配列が編集化である場合、追加したViewはページ内でレンダリングされ、削除したviewはDOMから削除される。

	var container = Ember.ContainerView.create();
	container.append();
	var coolView = App.CoolView.create(),
	childViews = container.get('childViews');
	childViews.pushObject(coolView);

短縮系として子になるviewをプロパティとして指定することもできるし、キーのリストとして指定することもできる。container viewが生成される際にこれらのviewは初期化され子になるviewの配列に追加される:

	var container = Ember.ContainerView.create({
		childViews: ['firstView', 'secondView'],
		firstView: App.FirstView,
		secondView: App.SecondView
	});

### renderメソッドの供給元

ViewがDOM要素として変換される以前に、viewは文字列として表現されているに過ぎない。viewがレンダリングされるごとにそれぞれの子になるviewを文字列にし結合している。

もしHandlebars以外のテンプレートエンジンを利用する場合は、viewの`render`メドッドを上書きすることでHTMLのカスタム文字列を生成できる。

	App.CoolView = Ember.View.create({
		render: function(buffer) {
			buffer.push("<b>This view is so cool!</b>");
		}
	});

こうすることでHandlebars以外のテンプレートエンジンをサポートすることを可能にしているが、レンダリングを上書きすることで値の自動アップデートはされないので注意が必要だ。すべてのアップデートは自分で実装する必要がある。

### HTML要素のカスタマイズ

1つのviewは1つのDOM要素としてページ内で表現される。`tagName`プロパティを変更することでどの要素が生成されるかを変更できる。

	App.MyView = Ember.View.extend({
		tagName: 'span'
	});

また同じようにクラス名も`className`プロパティを配列文字列として指定することで変更することができる:

	App.MyView = Ember.View.extend({
		classNames: ['my-view']
	});

viewのプロパティの状態に応じてクラス名を決定したい場合には、クラス名のバインディングを利用することができる。もしブーリアン値にバインドすればクラス名は値に応じて追加されるか削除される:

	App.MyView = Ember.View.extend({
		classNameBindings: ['isUrgent'],
		isUrgent: true
	});

上記の例をレンダリングすると下記のようになる:

	<div class="ember-view is-urgent">

もし`isUrgent`がfalseに変更されれば、クラス名`is-urgent`は削除される。

ブーリアン値を持つプロパティにダッシュをつけたクラス名が追加されるが、コロンを利用してクラス名を変更することもできる:

	App.MyView = Ember.View.extend({
		classNameBindings: ['isUrgent:urgent'],
		isUrgent: true
	});

以下のHTMLにレンダリングされる:

	<div class="ember-view urgent">

バインドされた値が文字列の場合、変更なしに値がクラス名として追加される。

### View内の属性バインディング

`attributeBindings`を利用してviewを表現するDOM要素の属性をバインディングできる。

	App.MyView = Ember.View.extend({
		tagName: 'a',
		attributeBindings: ['href'],
		href: "http://emberjs.com"
	});


## THE EMBER ENUMERABLE API

### Enumerablesとは？

Ember内ではEnumerablesとは子オブジェクトをいくつも持つオブジェクト全てで、それら子オブジェクトに対してEnumerablesインターフェイスを通じアクセスできるようになることを指す。もっとも基本的なEnumerablesはJavaScriptのビルトイン配列だ。

例えばすべてのEnumerablesはスタンダードである`forEach`メソッドをサポートしている:

	[1,2,3].forEach(function(item) {
		console.log(item);
	});

一般的には`forEach`のようなEnumerablesメソッドは2つ目の必須ではない引数を受け入れ、コールバック関数内において`this`の値となる:

	var array = [1,2,3];
	array.forEach(function(item) {
		console.log(item, this.indexOf(item));
	}, array)

ほかにも理由はあるが、こうすることでほかのEnumerablesなメソッドを`forEach`のコールバックとして利用することができるようになる点は非常に便利だ:

	var array = [1,2,3];
	array.forEach(array.removeObject, array);

注意: この第二引数はこのようにメソッドを使った場合のJavaScriptが`this`の値を`window`に割り当てるという挙動のワークアラウンドになる。

### Ember内のEnumerables

Emberオブジェクト内でリストを表すオブジェクトは大概の場合Enumerablesインターフェイスを実装している。いくつか例を見てみよう:

- *配列*: EmberはネイティブJavaScriptの配列をEnumerablesインターフェイスを通して拡張している。
- *配列プロキシ*: ネイティブの配列をラップしてviewレイヤ用に追加の機能を与える構成概念。
- *Set*: オブジェクトがオブジェクトを含むかどうかに対して素早く回答する。

### Enumerablesインターフェイス

#### パラメータ

Enumerablesメドッドのコールバックは以下の3つの引数を取る:

- *item*: 現在のイテレーションのアイテム。
- *index*: 整数値。ゼロからカウントされる。
- *self*: Enumerablesそのもの。

#### 羅列

Enumerableオブジェクトの全ての値を列挙するには`forEach`メソッドを利用する:

	enumerable.forEach(function(item, index, self) {
		console.log(item);
	});

Enumerableオブジェクトの書く要素であるメドッドを実行したい場合には`invoke`メソッドを利用する:

	Person = Ember.Object.extend({
		sayHello: function() {
			console.log("Hello from " + this.get('name'));
		}
	});
	var people = [
	Person.create({
		name: "Juan"
	}), Person.create({
		name: "Charles"
	}), Person.create({
		name: "Majd"
	})]
	people.invoke('sayHello');
	// Hello from Juan
	// Hello from Charles
	// Hello from Majd

#### Firstとlast

`firstObject`か`lastObject`を利用してEnumerableから始めと最後のオブジェクトを取得できる:

	[1,2,3].get('firstObject') // 1
	[1,2,3].get('lastObject')  // 3

#### 配列への変換

`toArray`メソッドを使ってEnumerableから配列を生成できる:

	['Goodbye', 'cruel', 'world'].map(function(item, index, self) {
		return item + "!";
	});
	// returns ["Goodbye!", "cruel!", "world!"]	

#### 各オブジェクトのsetとget

`forEach`や`map`は各要素のプロパティをgetまたはsetする際によく利用される。`getEach`と`setEach`メソッドを利用してその目的を果たすことができる:

	var arr = [Ember.Object.create(), Ember.Object.create()];
	// we now have an Array containing two Ember.Objects
	arr.setEach('name', 'unknown');
	arr.getEach('name') // ['unknown', 'unknown']

#### フィルタリング

Enumerableに対してよく行うタスクとしてはEnumerableをインプットとし、特定のルールによってフィルタリングされた配列を戻すという実装だ。

フィルタリングには(予想通りに)`filter`メソッドを利用できる。このメソッドはコールバック内らら`true`を返すことを期待している。コールバックが`true`を返せばEmberは最終的な配列内にその値を格納し、`false`あるいは`undefined`になる場合はEmberはその値を配列に格納しない。

	var arr = [1,2,3,4,5];
	arr.filter(function(item, index, self) {
		if (item < 4) { return true; }
	})
	// returns [1,2,3]

Emberオブジェクトのコレクションを操作する場合、あるプロパティの値を参考にオブジェクトのセットをフィルタしたいと考えるだろう。そのような場合には`filterProperty`メソッドがショートカットを提供してくれている。

	Todo = Ember.Object.extend({
		title: null,
		isDone: false
	});
	todos = [
	Todo.create({
		title: 'Write code',
		isDone: true
	}), Todo.create({
		title: 'Go to sleep'
	})];
	todos.filterProperty('isDone', true);
	// returns an Array containing just the first item

マッチした値を含む配列を戻すのではなく始めにマッチした値を戻したい場合には、`find`と`findProperty`メソッドを利用できる。これらは`filter`と`filterProperty`と同じように動作するが、1つのアイテムしか戻さない。

#### 情報のアグリゲーション(all または any)

Enumerableにある全てのアイテムが特定の条件に合致するかを調べるためには`every`メソッドが利用できる:

	Person = Ember.Object.extend({
		name: null,
		isHappy: false
	});
	var people = [
	Person.create({
		name: 'Yehuda',
		isHappy: true
	}), Person.create({
		name: 'Majd',
		isHappy: false
	})];
	people.every(function(person, index, self) {
		if (person.get('isHappy')) {
			return true;
		}
	});
	// returns false

またはEnumerable内の最低でも1つのアイテムがある条件にマッチするかを調べるには`some`メソッドが利用できる:

	people.some(function(person, index, self) {
		if(person.get('isHappy')) { return true; }
	});
	// returns true

フィルタリングのメソッドと同じく、`every`と`some`メソッドは`everyProperty`と`someProperty`という類似のメソッドを持っている:

	people.everyProperty('isHappy', true) // false
	people.someProperty('isHappy', true)  // true
