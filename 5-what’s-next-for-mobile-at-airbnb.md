# [What’s Next for Mobile at Airbnb](https://medium.com/airbnb-engineering/whats-next-for-mobile-at-airbnb-5e71618576ab)

### Exciting Times Ahead

React Nativeに挑戦している間も、我々はNative側での努力も怠ること無く継続していました。今日ではいくつものエキサイティングな、プロダクトに導入済の(あるいはもうすぐ導入される)プロジェクトが存在しています。そのうち幾つかのプロジェクトはReact Nativeの良い部分や我々の経験を元に開発されています。

#### Server-Driven Rendering

React Nativeを使っていないとしても、私達はプロダクトコードを"一度だけ"書くことに価値を見出しています。私達はDLS(Airbnbの社内デザイン言語)を多用しており多くの画面においてAndroidとiOSでほぼ同じデザインを適用しています。

幾つかのチームで実験を行い、パワフルなサーバドリブンのレンダリングフレームワークを統一する事を開始しました。このフレームワークにより、サーバーはデバイスに描画すべきコンポーネント、スクリーン設定、発火するアクションを記述したデータを送信し、各プラットフォームはデータを解釈しnativeの画面を描画したりDLSを用いた全体のフローを描画します。

大規模なサーバードリブンレンダリングは幾つかのチャレンジとセットになっています。我々が解決しようとしている一部を紹介すると:

- 後方互換性を維持したまま安全にコンポーネント定義をアップデートする
- プラットフォームを跨いで型定義を共有する
- 実行時のイベントに応答する(ボタンのタップやユーザーインプットなど)
- JSONドリブンなスクリーンの遷移を内部状態を保持しながら行う
- ビルド時に存在しないカスタムコンポーネントを描画する  
我々は[Lona](https://github.com/airbnb/Lona/)というformatを用いてこれを実験しています

サーバードリブンレンダリングフレームワークは機能をOTAで変更したりテストしたりすることができるという意味で既に我々に大きな価値をもたらしています。

### Epoxy Components

2016年に、我々は[Epoxy](https://github.com/airbnb/epoxy)をAndroid向けにオープンソース化しました。
Epoxyは多様なcellを持つRecyclerViews, UICollectionViews, UITableViewsを可能にするフレームワークで、殆どの新しい画面にはEpoxyを利用しています。Epoxyを利用する事でそれぞれのスクリーンを独立したcomponentとして定義する事が可能になり、遅延初期化も実施する事ができます。今日では私達はiOS/Androidの両方のEpoxy実装を公開しています。

iOSではコードは以下の様な形となります。

```swift
BasicRow.epoxyModel(
  content: BasicRow.Content(
    titleText: "Settings",
    subtitleText: "Optional subtitle"),
  style: .standard,
  dataID: "settings",
  selectionHandler: { [weak self] _, _, _ in
    self?.navigate(to: .settings)
  })
```

Androidでは、KotlinのDSLの機能を利用し型安全に簡潔にコンポーネントを実装できます。

```kotlin
basicRow {
 id("settings")
 title(R.string.settings)
 subtitleText(R.string.settings_subtitle)
 onClickListener { navigateTo(SETTINGS) }
```

### Epoxy Diffing

Reactでは、renderメソッドはコンポーネントのリストをreturnします。Reactのパフォーマンスの肝はそれらのコンポーネントは単に描画したいViewやHTMLのモデルとして表現できるという事です。コンポーネントのツリーはdiffが計算され必要な変更のみが実施されます。我々はEpoxyにおいて似たようなコンセプトを導入しました。EpoxyではbuildModelsメソッドの中にスクリーン全体の(を表現する)モデルを宣言します。Kotlinの優雅なDSLで表現されるその実装はReactとコンセプト的にとても似ており以下の様になります。

```kotlin
override fun EpoxyController.buildModels() {
  header {
    id("marquee")
    title(R.string.edit_profile)
  }
  inputRow {
    id("first name")
    title(R.string.first_name)
    text(firstName)
    onChange { 
      firstName = it 
      requestModelBuild()
    }
  }
  // Put the rest of your models here...
}
```

データが変更されると、`requestModelBuild()`が呼び出されRecyclerViewのメソッドが最適な形で呼び出され画面が再描画されます。

iOSではコードは以下の様になります。

```swift
override func itemModel(forDataID dataID: DemoDataID) -> EpoxyableModel? {
  switch dataID {
  case .header:
    return DocumentMarquee.epoxyModel(
      content: DocumentMarquee.Content(titleText: "Edit Profile"),
      style: .standard,
      dataID: DemoDataID.header)
  case .inputRow:
    return InputRow.epoxyModel(
      content: InputRow.Content(
        titleText: "First name",
        inputText: firstName)
      style: .standard,
      dataID: DemoDataID.inputRow,
      behaviorSetter: { [weak self] view, content, dataID in
        view.textDidChangeBlock = { _, inputText in
          self?.firstName = inputText
          self?.rebuildItemModel(forDataID: .inputRow)
        }
      })
  }
}
```

### A New Android Product Framework (MvRx)

最近最もエキサイティングな開発の一つは私達が内部で開発を進めているMvRxと呼ばれるフレームワークです。MvRxはEpoxy、Jetpack、RxJava、KotlinとReactから得た多くの原則を組み合わせ新しい画面の構築を今までにないほどより簡単にシームレスにします。これはReactのベストプラクティスと我々が発見した共通の実装パターンから生み出された独自のフレキシブルフレームワークです。MvRxはスレッドセーフでありほとんどの処理はメインスレッド外で動作する為スクロールやアニメーションは非常にスムーズに動作します。

これまでの所、MvRxは多くのスクリーンで動作しライフサイクルと付き合う必要性を殆ど取り除いてきました。私達はいくつかのAndroidプロダクトでMvRxを試しておりうまくいくようであればOSS化する事を計画しています。以下はネットワークリクエストを必要とする画面を構築する為のコードです。

```kotlin
data class SimpleDemoState(val listing: Async<Listing> = Uninitialized)

class SimpleDemoViewModel(override val initialState: SimpleDemoState) : MvRxViewModel<SimpleDemoState>() {
    init {
        fetchListing()
    }

    private fun fetchListing() {
        // This automatically fires off a request and maps its response to Async<Listing>
        // which is a sealed class and can be: Unitialized, Loading, Success, and Fail.
        // No need for separate success and failure handlers!
        // This request is also lifecycle-aware. It will survive configuration changes and
        // will never be delivered after onStop.
        ListingRequest.forListingId(12345L).execute { copy(listing = it) }
    }
}

class SimpleDemoFragment : MvRxFragment() {
    // This will automatically subscribe to the ViewModel state and rebuild the epoxy models
    // any time anything changes. Similar to how React's render method runs for every change of
    // props or state.
    private val viewModel by fragmentViewModel(SimpleDemoViewModel::class)

    override fun EpoxyController.buildModels() {
        val (state) = withState(viewModel)
        if (state.listing is Loading) {
            loader()
            return
        }
        // These Epoxy models are not the views themself so calling buildModels is cheap. RecyclerView
        // diffing will be automaticaly done and only the models that changed will re-render.
        documentMarquee {
            title(state.listing().name)
        }
        // Put the rest of your Epoxy models here...
    }

    override fun EpoxyController.buildFooter() = fixedActionFooter {
        val (state) = withState(viewModel)
        buttonLoading(state is Loading)
        buttonText(state.listing().price)
        buttonOnClickListener { _ -> }
    }
}
```

MvRxはFragmentの引数の処理、プロセスを跨いだsavedInstanceStateによる永続化、TTIのトラッキングなどに関してシンプルな概念を持っており他にも多くの機能があります。

iOSでもまだβテストの段階ですが似たフレームワークを開発しています。

これらについて近々さらに情報を公開する予定ですが、これまで積み上げてきた成果に我々はとても興奮しています。

### Iteration Speed

React Nativeから移行してきて明らかな問題の一つが開発サイクルのスピードです。1,2秒で変更をテストできる世界から15分も待たないといけない世界へ戻ってくるのは厳しいですよね。幸運な事に、我々はこちらに関しても殆ど必要とされている支援を提供する事ができました。

私達はAndroidとiOSでアプリの一部のみをコンパイルし、特定のモジュールに依存する(launcherを含む部分的な)アプリを生成するような基盤を構築しています。

Androidでは、Gradleのプロダクトフレーバーを利用して実現しています。gradleモジュールは以下の様になります。

![](https://cdn-images-1.medium.com/max/1600/1*KVrbsdwESyfbtKFeh2acXg.png)

この新しい依存関係の指定はエンジニアにアプリを部分的にビルドする事を可能にします。IntelliJの[module unloading](https://blog.jetbrains.com/idea/2017/06/intellij-idea-2017-2-eap-introduces-unloaded-modules/)という機能と合わせる事でMac book Pro上で劇的にビルドとIDEのパフォーマンスが改善しました。

私達は新しいテスト用フレーバーを生成するスクリプトを開発し数ヶ月の間に20以上のフレーバーを作成してきました。新しいフレーバーを利用した開発速度は平均2.5倍高速化し5分以上かかっていたビルド時間は15倍改善しました。

参考までに、[こちら](https://gist.github.com/gpeal/d68e4fc1357ef9d126f25afd9ab4eee2)が動的に(root moduleへの参照を持つ)プロダクトフレーバーを生成するスニペットです。

似たようにiOSではmoduleは以下の様になります。

![](https://cdn-images-1.medium.com/max/1600/1*AVB7em_JCmj-JmjTCkLdQw.png)

同じシステムでビルドは3~8倍高速化しました。

### Conclusion

会社にとって、新しい技術に挑戦する事を恐れず、高い品質、開発速度、開発者体験を維持する事はとても重要です。結論としては、React Nativeは機能をリリースし、モバイル開発にとって新しい考え方を私達にもたらす必要なツールでした。もしこの話を聞いてあなたも参加してみたいと思ったならば、ぜひ私達までご一報を！

# その他の記事
- [Part 1](https://medium.com/airbnb-engineering/react-native-at-airbnb-f95aa460be1c)（[日本語訳](https://github.com/react-native-jp/react-native-at-airbnb-jp-translation/blob/master/1-alt-react-native-at-airbnb.md)）: React Native at Airbnb
- [Part 2](https://medium.com/airbnb-engineering/react-native-at-airbnb-the-technology-dafd0b43838)（[日本語訳](https://github.com/react-native-jp/react-native-at-airbnb-jp-translation/blob/master/2-react-native-at-airbnb-the-technology.md)）: The Technology
- [Part 3](https://medium.com/airbnb-engineering/building-a-cross-platform-mobile-team-3e1837b40a88)（[日本語訳](https://github.com/react-native-jp/react-native-at-airbnb-jp-translation/blob/master/3-building-a-cross-platform-mobile-team.md)）: Building a Cross-Platform Mobile Team
- [Part 4](https://medium.com/airbnb-engineering/sunsetting-react-native-1868ba28e30a)（[日本語訳](https://github.com/react-native-jp/react-native-at-airbnb-jp-translation/blob/master/4-sunsetting-react-native.md)）: Making a Decision on React Native
- [Part 5](https://medium.com/airbnb-engineering/whats-next-for-mobile-at-airbnb-5e71618576ab)（[日本語訳](https://github.com/react-native-jp/react-native-at-airbnb-jp-translation/blob/master/5-what%E2%80%99s-next-for-mobile-at-airbnb.md)）: What’s Next for Mobile（本記事）
