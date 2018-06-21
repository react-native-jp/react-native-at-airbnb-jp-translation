この記事はAirbnb社におけるReact Native体験と `次の時代`のモバイルアプリケーション開発について記したブログシリーズの第二弾です。

React NativeはAndroid, iOS, Webで横断的に動作する、それ自体が比較的新しく急速に発展しているクロスプラットフォームフレームワークです。2年間に渡って使ってきた今、React Nativeは多くの点において革新的なツールであると自信を持って言えます。それはモバイル開発におけるパラダイムシフトであり、私たちはReact Nativeが掲げるゴールから多くの恩恵を受けることができました。しかしその利点を得るために多くの痛みが伴ったことは無視できません。

## 上手くいったこと
### クロスプラットフォーム
React Nativeを利用することによる一番の利点は一度書いたコードがAndroidでもiOSでも動作するということです。React Nativeで実装した機能の多くでは95-100%のコードを共有することができ、 プラットフォームに依存したファイル(\*.android.js/\*.ios.js)は0.2%にすぎませんでした。

### 共通のデザイン言語システム(DLS)
私たちは[DLS](https://airbnb.design/building-a-visual-language/)と呼ばれるクロスプラットフォームのデザイン言語を開発しており、Android、iOS、React Native、Web、それぞれのバージョンがコンポーネントごとに存在しています。クロスプラットフォームに開発を進める上で、一貫したデザイン、コンポーネント名、そして画面を定義できる共通のデザイン言語を持つことは当然の帰結でした。

しかし同時に必要に応じてプラットフォーム固有のデザインを適用することもまた可能でした。例えばAndroidにおいて[Toolbar](https://developer.android.com/reference/android/support/v7/widget/Toolbar)を利用するがiOSでは[UINavigationBar](https://developer.apple.com/documentation/uikit/uinavigationbar)を使う、Andoroidではプラットフォームのデザインガイドラインに適さない[disclosure indicators](https://developer.apple.com/ios/human-interface-guidelines/views/tables/)を隠すなどといったことです。

私たちはネイティブのコンポーネントをラップするのではなく、React Nativeでコンポーネントを書き直す方法を選択しました。なぜならその方が個別にプラットフォーム固有のAPIを叩く上で信頼性が高く、React Native上での変更をテストする手段に詳しくないネイティブエンジニアのメンテナンスにおけるオーバーヘッドを削減できるからです。ただしこのアプローチはネイティブとReact Native間でのバージョンの乖離を引き起こすこととなりました。

### React
Reactが[最も愛される](https://insights.stackoverflow.com/survey/2018/#technology-most-loved-dreaded-and-wanted-frameworks-libraries-and-tools)Webフレームワークと呼ばれるのには理由があります。シンプルかつ強力で、より大きなコードベースへスケールしやすいからです。特に私たちが気に入っている点としては、

- *Components:* 適切に定義されたpropsとstateによってReact Componentは開発における関心の分離を実現します。これはReactのスケーラビリティにおける大きな要因です。
- *簡潔なライフサイクル:* Androidと(多少はマシですが)iOSのライフサイクルは非常に複雑なことで知られています。適切に動作するリアクティブなReact Componentはこの問題を根本的に解決するため、React Nativeの学習をAndroidやiOSのそれと比べて非常に容易にしています。
- *宣言的:* Declarative(宣言的)であるというReactの特徴は、内部のStateとUIの整合性を保つ上でとても役に立ちます。

### 検証スピード
React Nativeを利用することでアプリケーションの変更を即座に確認できる[hot reloading](https://facebook.github.io/react-native/blog/2016/03/24/introducing-hot-reloading.html)を活用することができます。ビルド時のパフォーマンスこそネイティブアプリに軍配が上がりますが、開発・検証スピードにおいてはReact Nativeの足下にも及びません。最速で15秒もあればネイティブアプリをコンパイルすることはできますが、長い時にはビルドに20分ほどかかることもあります。

### インフラへの投資
私たちはネイティブのインフラに対して広い範囲でのIntegrationを実現しました。'networking', 'i18n', 'experimentation', 'shared element transitions', 'device info', 'account info'というコア機能をはじめとした多くの機能が個別のReact Native APIとしてラップされました。既にあるAndroidやiOSのAPIをReactのために整合性を持たせたり正規化する必要があったため、それらのBrdigeはとても複雑なパーツとなりました。高速な開発サイクルの中でこれらのBridgeを常に最新の状態に保つ作業が必要になりましたが、インフラチームがこの部分に投資してくれたことによってプロダクト開発がずっと容易になりました。

このインフラ部分への大きな投資がなければ、React Nativeによる開発はもっと中途半端なものなっていたでしょう。React Nativeを既存のアプリにつっこむためには、このような投資が必要不可欠だろうというのが私たちの結論です。

### パフォーマンス
React Nativeにまつわる最も大きな心配事の一つがパフォーマンスの問題です。ですが実際はほとんど問題になることはなく、React Nativeで実装した多くの画面がネイティブと遜色なく動作しました。パフォーマンスというものは一面的に捉えられがちです。ネイティブエンジニアがJSに対して「JAVAより遅い」と考えているのをよく見かけますが、ビジネスロジックや[layout](https://github.com/facebook/yoga)をメインスレッドから分割することで描画のパフォーマンスが向上することは多々あります。

パフォーマンスの問題が発生することもありましたが、大抵は過度な描画によるもので[shouldComponentUpdate](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)や[removeClippedSubviews](https://facebook.github.io/react-native/docs/view.html#removeclippedsubviews)を活用したりReduxの使い方を改善することで和らげることができました。

しかしながら初期化処理と最初の描画時間に関するパフォーマンスは低くReact Nativeで作った起動画面やディープリンク、画面回遊時のTTIなどに大きな影響を与えました。それに加えて[Yoga](https://github.com/facebook/yoga)がReact Native coponentをネイティブのviewに変換するため、スクリーンフレームの低下時にデバッグするのが難しいという問題もありました。

### Redux
StateとUIの整合性を保ちやすい、画面をまたいだデータの共有が簡単に行える等の有用性から私たちはReduxを採用しました。ですがReduxは悪名高いboilerplateで知られ、学習の難易度も高いです。一般的なテンプレートを生成するジェネレータも用意しましたが、ReduxはReact Nativeで開発する上で最も難しい部分であり混乱を招きやすい部分であることに変わりはありませんでした。React Nativeに限った問題ではありませんが。

### ネイティブの支え
React Nativeの中で起きる全てのことはネイティブのコードにBridgeできるため、開発を初めた時点では予期しなかった多くのことが可能になりました。

1. *Shared element transitions:* 私たちはAndroidとiOSのネイティブコードと連携した `<SharedElement>`コンポーネントを開発しました。これはネイティブとReact Nativeのスクリーン間でも利用できます。
2. *Lottie:* AndroidとiOS向けのライブラリをラップすることでLottieをReact Native上で動かすことが可能になりました。
3. *ネットワーク機構:* React Nativeは既にあるネイティブの通信スタックとキャッシュ機構を利用します。
4. *その他のコアインフラ:* Networkingだけでなく、'i18n'や'experimentation'のようなその他のネイティブインフラもラップすることでReact Nativeからシームレスに利用できます。

### 静的解析
AirbnbはWebでの[eslintの利用](https://github.com/airbnb/javascript)について確固とした実績を持っていますが、[prettier](https://github.com/prettier/prettier])を導入したのは私たちのプラットフォームが社内では初めてでした。prettierはPull Requestにおける細かな指摘やミスを軽減するのにとても有効で、Webインフラチームでは活発に試されています。

また描画にかかる時間とコストを解析することで、改修の優先度が高い画面を調べています。

React NativeはWebと似ているだけでなくそのさらに先を行くプラットフォームであるため、新しいアイディアを試すのに持ってこいです。React Nativeのために開発した多くのツールやアイディアが今ではWebにも持ち込まれています。

### アニメーション
React Nativeの[アニメーションライブラリ](https://facebook.github.io/react-native/docs/animated.html)のおかげで効果的なアニメーションを実装可能になり、スクローリングやパララックスなどインタラクションドリブンなアニメーションも手に入れました。

### オープンソース
React NativeではReactとJavaScriptという巨人の肩に乗ることで、redux,reselect, jestといった膨大な数のJSプロジェクトの恩恵を受けることができます。

### Flexbox
C言語で書かれたクロスプラットフォームなライブラリであり[Flexbox](https://www.w3schools.com/css/css3_flexbox.asp) APIによるレイアウト計算を行う[Yoga](https://github.com/facebook/yoga)をReact Nativeでは利用しています。はじめこそアスペクト比が使えないなどYogaの機能的制限に悩まされましたが、その後のアップデートで解決されました。[flebox froggy](https://flexboxfroggy.com/)のような楽しいチュートリアルもオンボーディングで活躍しています。

### Webとのコラボレーション
開発が進むにつれて最近ではWeb, iOS, Androidの開発を一度に行えるようになりました。WebでもReduxが使われていることからWebとネイティブで広範囲に渡るコードを、特別な調整なしで共有できることに気が付いたのです。

## 上手くいかなかったこと
### React Nativeの未熟さ故の問題
新しく、野心的で、急速に発展してはいますがReact NativeはAndroidやiOSに比べてまだまだ未成熟なプラットフォームです。多くの状況でReact Nativeは上手く機能する一方、その未熟さ故に些細な問題を解決するのがとても難しくなってしまうことがあります。残念ながらこれらのケースは予測が難しく、ワークアラウンドを行うのに数時間で終わることもあれば何日もかかることもあります。

### Forked React Nativeのメンテナンス
React Nativeは未成熟であるため場合によってはソースコードに修正を加える必要があります。すぐに修正を反映するためにはReact Native本体にコントリビュートするのみでなく、Forkした[独自のレポジトリ](https://github.com/airbnb/react-native/commits/0.46-canary)を用意して管理する必要がありました。この2年間でReact Nativeに対して約50個ほどコミットを積みましたが、これによってReact Nativeのアップデートが恐ろしく大変なものとなったのです。

### JavaScriptツール
JavaScriptは動的型付けの言語です。型安全でない言語を扱うことはスケールが難しいだけでなく、型のある世界からやってきたネイティブエンジニアとの争いの元となりました。彼らは型の問題がなければよりReact Nativeの学習に興味を示してくれたでしょう。

私たちは[flow](https://flow.org/)の導入を試みましたが、まるで暗号のようなエラーメッセージに開発者のフラストレーションはたまる一方でした。[TypeScript](http://www.typescriptlang.org/)も調査はしましたが、既にある[babel](https://babeljs.io/)や[metro bundler](https://github.com/facebook/metro)との統合に問題があります。そのような状況ではありますが、今後も私たちはTypeScriptの調査を継続していくつもりです。

### リファクタリング
JavaScriptという型のない言語を扱った結果、リファクタリングがとても大変な作業になり多くのエラーが吐かれることとなりました。props名の変更、特に複数のコンポーネントにまたがって継承される `onClick`のような広く使われるpropsを正確に変更する作業はまるで悪夢のようでした。更に悪いことにコンパイルは通るのにプロダクション環境で壊れるリファクタ、静的解析を適用するのが難しいリファクタなどもありました。

### JavaScriptCoreの不安定性
React Nativeのトリッキーな特徴としては、それが[JavaScriptCore environment](https://facebook.github.io/react-native/docs/javascript-environment.html)で実行されるということです。それによって以下のような問題に直面しました。

- iOSは初期状態で[独自のJavaScriptCore](https://developer.apple.com/documentation/javascriptcore)を持っているため動作も概ね安定しており、大きな問題はありませんでした。
- Androidは自前のJavaScriptCoreを持たないため、React Nativeが自身のものをバンドルすることで実行環境が作られます。問題なのはこのランタイムがとてつもなく[古い](https://github.com/facebook/react-native/issues/10245)ものであるということです。結果的に私たちは新たに自前で新しい[JSC](https://github.com/react-community/jsc-android-buildscripts)をバンドルしてやる必要がありました。
- React Nativeはデバッグ時にパワフルなChrome Developer Toolsに接続します。素晴らしい！ですがデバッグモードに入ると同時に、React NativeのコードはChromeのV8エンジンで実行されるようになります。99.9%のケースでは特に問題はないのですが、ただ一度だけ、 `toLocaleString`メソッドがAndroidではデバッグモードでしか動かないという問題に引っかかりました(iOSは大丈夫でした)。どうやらAndroidのJavaScriptCoreにメソッドが[含まれておらず](https://github.com/facebook/react-native/issues/15717)、そこでエラーも吐かずに落ちていたようです。当然デバッグ時に利用するV8環境では動作していました。このような問題に対処できる深い技術的知識がなければ、何日も現場のエンジニア達は悩まされることになるでしょう。

### React Native向けのオープンソースライブラリ
任意のプラットフォームについて学び、習熟するのには長い時間がかかります。多くの人は1つまたは2つのプラットフォームについてのみしか理解してないと言えるでしょう。mapやvideoといったネイティブとのbrdigeを持ったReact Nativeライブラリを開発するには3つ全てのプラットフォーム知識が求められます。残念ながら多くのReact Native向けオープンソースライブラリの開発者は十分な数のプラットフォーム知識を有しているとは言えないでしょう。それはAndroidとiOSどちらかのプラットフォーム上では動作が不安定になることを意味します。

Android向けの多くのReact Nativeライブラリではパッケージの読み込みにmavenではなくnode modulesの相対パスによる記述を要求しますが、これはコミュニティの期待する品質に比べて安定性が低いやりかたです。

### インフラの二重管理と機能開発
私たちは長年に渡ってAndroidとiOSにおけるネイティブのインフラストラクチャを構築・蓄積してきましたが、React Nativeに向けて全ての既存のインフラに対するBridgeをゼロから作成する必要がありました。つまりエンジニアが新しい機能を開発する際に、よく理解していないプラットフォームに対しても自らの手でBridgeを用意するか、誰かがそれを作ってくれるまで待つ必要があったのです。

### クラッシュレポート
私たちはネイティブランタイムでのクラッシュレポートに[Bugsnag](https://www.bugsnag.com/)を利用しています。AndroidとiOSのどちらのプラットフォームでも大抵は動作する一方で、あまり信憑性は高いとは言えず特定にプラットフォームに向けたオプティマイゼーションも必要でした。React Nativeは新興であるが故に、私たち自身の手でsource mapアップロードのようなインフラを大量に作成したり、BugsnagがReact Nativeで起きた問題のみをトラックできるようなフィルタリング機能を追加する必要がありました。

このように多くのインフラレベルの機能をReact Nativeに実装しなければいけない状況下であるため、クラッシュしたのにレポートが送信されない、source mapが適切にアップロードされないといった問題に悩まされることになりました。

もう一つ付け加えると、ネイティブとReact Native間でスタックトレースは別れてしまうため、双方を跨いだ問題のデバッグはとても大変なものになるでしょう。

### ネイティブブリッジ
React Nativeにはネイティブと通信するための[bridge API](https://facebook.github.io/react-native/docs/communication-ios.html)があります。このAPIは期待どうりの動作をしてくれるものの、実装するのがものすごく面倒です。まず初めに3つのプラットフォーム向けのそれぞれの開発環境を適切に設定する必要があります。JavaScriptから返ってくるデータ型が予測不可能という問題もあります。例えばinteger型はしばしばstring型にラップされてしまい、bridgeを通ってみるまでどうなるのか分かりません。またAndroidではクラッシュするのにiOSではフィードバックもなく落ちることがあります。私たちは2017年の終わりにかけてTypeScriptの型定義ファイルからbridgeコードを自動生成する方法に取り組みましたが、その時にはもう遅すぎました。

### 初期化の時間
React Nativeがファーストビューを描画するには、まずその前にランタイムを初期化する必要があります。残念ながら私たちほどの規模になると、たとえそれがハイエンドデバイスであっても数秒かかってしまいます。このような問題があるなかでReact Nativeをアプリの起動画面に採用することはほぼ不可能です。私たちはアプリの起動時にReact Nativeの初期化を行うことでファーストビューの描画にかかる時間を最小化しました。

### ファーストビューの描画時間
ネイティブの場合とは異なりReact Nativeでファーストビューに必要な情報をあつめて描画するためには、メインスレッド -> JS -> yogaレイアウトスレッド -> メインスレッドというサイクルを実行する必要があります。ファーストビューを描画するまでの平均時間はiOSで280ms、Androidで440msでした。Androidでは[postponeEnterTransition API](https://developer.android.com/reference/android/app/Activity.html#postponeEnterTransition%28%29)を利用しています。通常はshared element transitionにおいて描画までのディレイを適用するために使われるものです。iOSにおいてはNavBarの設定にReact Nativeだと時間がかかりすぎるという問題がありました。最終的に私たちはnavbarの設定が読み込まれるまではインタラクションが発生しないよう、全てのReact Nativeでのページ遷移に50msのディレイをかけることにしました。

### App Size
React Nativeによるアプリケーションサイズへの影響も無視できません。Androidの場合、React Nativeのトータルのサイズ(Java + JS + Yoga含むネイティブライブラリ + JavaScript Runtime)はABIごとに8MBとなりました。x86とarm(32bit only)を含めたAPKなら12MBほどになるでしょう。

### 64bit
[この](https://github.com/facebook/react-native/issues/2814)問題により、今の所はまだAndroidに向けて64bitのAPKを配信できていません。

### ジェスチャー
タッチコントロールに関するサブシステムはAndroidとiOS間での違いが大きすぎることもあり、それらを統合するAPIを開発することはReact Nativeコミュニティ全体にとって大きな課題です。そのようなこともあり現在Airbnbでは、複雑なジェスチャーを要求する画面ではReact Nativeは利用していません。しかしながら[react-native-gesture-handler](https://github.com/kmagiera/react-native-gesture-handler)のv1.0が最近リリースされるなど、まだ取り組みは活発なようです。

### リスト表示
[FlatList](https://facebook.github.io/react-native/docs/flatlist.html)のようなライブラリが登場したことでReact Nativeでもこの分野が発展してきているようです。ですがそれはまだAndroidの[RecyclerView](https://developer.android.com/guide/topics/ui/layout/recyclerview)やiOSの[UICollectionView](https://developer.apple.com/documentation/uikit/uicollectionview)には到底及びません。スレッドの機構により多くの制限がなかなか解決されないでいます。Adapterの持つデータには同期的なアクセスされないため、高速にスクロールしていても非同期でViewを描画することも可能なはずです。テキストもiOSでは非同期で取得されるため、事前にCellの高さを計算することが難しいです。

(* 訳注: あまり意味が理解できておらず申し訳ないです。)

### React Nativeのアップグレード
React Nativeにおけるほとんどのアップグレード作業は難しくありませんが、とても大変なケースがいくつかありました。具体的な例を挙げるとReact Native 0.43(2017年4月リリース)からReact Native 0.49(2017年10月リリース)へのアップグレード作業では、前者がReact 16のα版、後者がβ版を利用していた関係でほぼ不可能とも思える作業になりました。多くのReactライブラリがWebを想定して設計されているため、最新版のReactへの対応がなされておらず手こずることとなりました。2017年中頃のインフラチームはこの依存解決の問題に追われ、結果的に大くのコストが費やされたこととなります。

### アクセシビリティ
2017年に私たちはアクセシビリティに関する[大幅な改修](https://airbnb.design/designing-for-access/)を行い、ハンディキャップを抱えた人たちが最適なリスティングを見つけ、予約できるように努力しました。しかしながらReact NativeのアクセシビリティAPIにはいくつもの穴があります。最低限のアクセシビリティ要件を満たすためだけでも、私たちは[Forkした](https://github.com/airbnb/react-native/commits/0.46-canary)React Nativeをメンテナンスして修正をマージする必要がありました。AndroidやiOSならば1行加えるだけですむような修正でも、React Nativeでの実装方法を考え、変更を加え、React Native Coreに修正を出してケアするのには膨大な時間がかかりました。

### やっかいなクラッシュ
私たちはいくつかの修正が難しい奇妙な問題にもぶつかりました。例をあげると[こちら](https://issuetracker.google.com/issues/37045084)の@ReactPropに関する問題を現在も調査中で、同じ端末同じソフトウェア上であっても問題を再現・究明できずにいます。

### Androidでのプロセスの永続化
Androidは頻繁にバックグラウンドプロセスをリフレッシュしますが、その対策としてアプリケーションバンドルに[stateを同期的に保存](https://developer.android.com/topic/libraries/architecture/saving-states#use_onsaveinstancestate_as_backup_to_handle_system_initiated_process_death)することが可能です。しかしながらReact Nativeにおいては全てのstateに対してJSからしかアクセスできないため、この同期的な保存を利用することができません。もしできたとしてもReduxにはシリアライズ可能なデータと不可能なデータが混在していますし、savedInstanceStateの容量では足りない大きさのデータも含まれるため、どちらにせよプロダクションで[クラッシュ](https://medium.com/@mdmasudparvez/android-os-transactiontoolargeexception-on-nougat-solved-3b6e30597345)してしまいます。


# その他の記事
- [Part 1](https://medium.com/airbnb-engineering/react-native-at-airbnb-f95aa460be1c)（[日本語訳](https://github.com/react-native-jp/react-native-at-airbnb-jp-translation/blob/master/1-alt-react-native-at-airbnb.md)）: React Native at Airbnb
- [Part 2](https://medium.com/airbnb-engineering/react-native-at-airbnb-the-technology-dafd0b43838)（[日本語訳](https://github.com/react-native-jp/react-native-at-airbnb-jp-translation/blob/master/2-react-native-at-airbnb-the-technology.md)）: The Technology(本記事)
- [Part 3](https://medium.com/airbnb-engineering/building-a-cross-platform-mobile-team-3e1837b40a88)（[日本語訳](https://github.com/react-native-jp/react-native-at-airbnb-jp-translation/blob/master/3-building-a-cross-platform-mobile-team.md)）: Building a Cross-Platform Mobile Team
- [Part 4](https://medium.com/airbnb-engineering/sunsetting-react-native-1868ba28e30a)（[日本語訳](https://github.com/react-native-jp/react-native-at-airbnb-jp-translation/blob/master/4-sunsetting-react-native.md)）: Making a Decision on React Native
- [Part 5](https://medium.com/airbnb-engineering/whats-next-for-mobile-at-airbnb-5e71618576ab)（[日本語訳](https://github.com/react-native-jp/react-native-at-airbnb-jp-translation/blob/master/5-what%E2%80%99s-next-for-mobile-at-airbnb.md)）: What’s Next for Mobile
