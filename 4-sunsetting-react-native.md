# はじめに
この記事は2018年6月20日にAirbnb社の[Gabriel Peal](https://medium.com/@gpeal)によって公開された記事を日本語に翻訳したものです。

公開にあたっては氏本人の同意を確認中ですが、誤訳や問題のある表現等を見つけた方は僕の[Twitterアカウント](https://twitter.com/andoshin11)までDMいただけますと幸いです。

元記事:

[https://medium.com/airbnb-engineering/sunsetting-react-native-1868ba28e30a:embed:cite]

# 本文
この記事はAirbnb社におけるReact Native体験と `次の時代`のモバイルアプリケーション開発について記したブログシリーズの第四弾です。

これまで多くのチームがReact Nativeを信頼して今後も利用し続ける計画を立てていましたが、結論としてReact Nativeが私たちのゴールを達成することはできませんでした。継続してReact Nativeに投資を続けるための数多くの[技術的な問題](https://medium.com/airbnb-engineering/react-native-at-airbnb-the-technology-dafd0b43838)や[組織構造の問題](https://medium.com/airbnb-engineering/building-a-cross-platform-mobile-team-3e1837b40a88)に対して、私たちは解決策を見出せなかったのです。

この経験を活かして前に進むため、AirbnbではReact Nativeから漸次的に退いて全ての力をネイティブに再投資します。

## 目標達成の失敗
### より早く動くこと
React Nativeが期待通りのパフォーマンスを発揮していた頃、エンジニアたちはかつてないスピードで開発を進めることができました。しかしこの一連の記事で触れている[技術的な問題](https://medium.com/airbnb-engineering/react-native-at-airbnb-the-technology-dafd0b43838)や[組織構造の問題](https://medium.com/airbnb-engineering/building-a-cross-platform-mobile-team-3e1837b40a88)によってフラストレーションが蓄積され、予見できなかった様々な遅延が多くのプロジェクトにおいて発生しました。

### 品質を保つこと
React Native自体の成熟度が上がったことや、私たちの中でより多くの知見が蓄積されたことなどで、近頃はかつて実現可能性すら分からなかった多くのことが可能になりました。Shared Element TransitionsやParallaxを作ったり、フレームレートの低下が頻繁に起きていた画面のパフォーマンスを劇的に向上させたりといったことです。しかしながら初期化処理や非同期なファーストビューの描画に関するものなどの[様々な技術的チャレンジ](https://medium.com/airbnb-engineering/react-native-at-airbnb-the-technology-dafd0b43838)によって叶わなかったものがあるのも事実です。社の内外におけるリソースの不足により、これらの問題は一層難しくなりました。

### Write Code Once Instead of Twice
私たちのアプリにおいてはReact Nativeの大部分が複数のプラットフォームで共有されているものの、そもそもReact Nativeが使われているのはアプリ全体のわずかな部分でしかありません。更に、プロダクトエンジニアの生産性を担保するためには大量のbridgingが必要になります。その結果として私たちは(2つではなく)3つ全てのプラットフォームに対する統合的な補助コードを作ることになりました。私たちはWebとネイティブにおけるコードシェアリングの可能性を信じ、実際にいくつかのNPMパッケージを共有することができましたが、それを除けば本当に意味のある"Write Code Once Instead of Twice"は実現しませんでした。

## 撤退計画
上記のようにReact Nativeは私たちが定めたゴールを達成することができなかったため、私たちに適した道具ではないということを結論づけました。目下、複数のチームと力を合わせて最適な移行プランを計画中です。既にReact Nativeを利用した全ての機能開発は取りやめ、高いトラフィックを持つ画面については年内を目標に大部分をネイティブに置き換え予定です。これらは必ずしもReact Nativeのみに責任があるわけではなく、予てより予定されていたスケジュール変更の影響もあります。私たちのネイティブインフラチームも2018年中はReact Nativeのサポートを行う予定です。2019年からは徐々にサポートを減少させ、起動時のランタイム初期化等のReact Nativeがオーバーヘッドする部分も削減してく予定です。

Airbnb社は、オープンソースの力と意義を信じています。私たちは積極的にOSSを利用するだけでなく、世界中の多くのプロジェクトにコントリビュートしており、React Nativeに関するものも公開してきました。社全体として徐々にReact Nativeから離れていくなかでReact Nativeレポジトリに対して十分なメンテナンスが行えなくなってしまいますが、コミュニティにとってのベストを考えていくつかのプロジェクトを[React Native Community](https://github.com/react-native-community)に移譲します。既に[react-native-maps](https://github.com/react-community/react-native-maps)については取り組みが始まっており、[native-navigation](https://github.com/airbnb/native-navigation)や[lottie-react-native](https://github.com/airbnb/lottie-react-native/)についても対応予定です。

## そんなに悪くなかったよ？
私たちのゴールを達成することこそ叶いませんでしたが、React Nativeに触れていたエンジニア達は概ね肯定的な意見です。内訳をみると、

- 60%が「素晴らしい開発経験だった」と感じた
- 20%がやや肯定的
- 15%がやや否定的
- 5%が強く否定

となっています。また63%のエンジニアがまたチャンスがあればReact Nativeを選択すると答え、74%が新しいプロジェクトにおいて選択肢に含めると答えました。注目すべき点は、これらの調査対象が全て一度はReact Nativeを使っていたエンジニアであるということです。

彼らは80,000行ものプロダクトコードを産み出し、220もの画面を作り、40,000行ものJavaScriptのインフラストラクチャを書いたエンジニアです。参考のために触れておくと、私たちはネイティブにおいてはこの10倍ものコードと4倍もの数の画面をそれぞれのプラットフォームで開発しています。

## React Nativeは成熟してきている
この一連のポストは今日の時点における私たちのReact Native体験を振り返るものです。ですがFacebookやReact Native CommunityのメンバーはRNがハイブリッドアプリでもスケールするよう身を砕いています。かつてないスピードでReact Nativeは発展しているのです。昨年には2,500ものコミットが反映され、Facebook自身も私たちが直面したような問題を真摯に[受け止めて](https://facebook.github.io/react-native/blog/2018/06/14/state-of-react-native-2018)います。私たちがこれ以上React Nativeに投資することはありませんが、これからの開発を見守れることを楽しみにしています。なぜならReact Nativeの成功は私たちのプロダクトを使ってくれる世界中の人々の幸せにつながるのですから。

## 学び
私たちは急速に発展を続けるアプリケーションにReact Nativeを組み込みました。その中で直面した問題の多くは、私たちがハイブリッドモデルのアプローチを選択したことに由来します。しかし小さな会社では十分なリソースが無く、このスケールの会社だったからこそ得られたことや解決した問題もあるでしょう。React Nativeをネイティブとシームレスに統合することは可能です。ですが難しいことです。React Nativeを使う全ての会社が、そのチームの構成や、既存のアプリや、プロダクトの要求や、React Nativeの成熟度によってユニークな経験をするのだろうと思います。

全てのピースがはまった時(そういう場面はたくさんありました)、React Nativeは開発のイテレーションスピードや品質、そして開発体験全体において私たちの期待通りもしくは期待以上のパフォーマンスを発揮してくれました。そういう時には、私たちはモバイル開発自体を変革させる瀬戸際に立っているのだというような気持ちになりました。もちろんそのような素晴らしい体験も是非していただきたいですが、改めてその恩恵と痛み、そして今の優先事項やエンジニアリング組織のリソースを比較したとき、それは私たちにふさわしいものではないと結論づけました。

新たなプラットフォームの採用はとても大きな決断で、あなたのチームが抱える様々なユニークなファクターによって決まります。私たちが経験したことやReact Nativeを手放すことになった理由は、あなたのチームには当てはまらないかもしれません。実際に[たくさんの会社](https://medium.com/@Pinterest_Engineering/supporting-react-native-at-pinterest-f8c2233f90e6)がReact Nativeによる素晴らしい体験を継続しており、多くの人々にとってはいまだにベストなツールかもしれません。

私たちはこれまでもネイティブに投資をし続けていましたが、React Nativeから離れることでかつてないほどのリソースをより良いアプリを作ることに投下できます。次の記事で私たちの新たなネイティブに関する取り組みを知ってください！


# その他の記事
- [Part 1](http://studio-andy.hatenablog.com/entry/react-native-at-airbnb): React Native at Airbnb
- [Part 2](http://studio-andy.hatenablog.com/entry/react-native-at-airbnb-the-technology): The Technology
- [Part 3](https://hotchemi.hateblo.jp/entry/2018/06/20/234054): Building a Cross-Platform Mobile Team
- Part 4: Making a Decision on React Native(本記事)
- Part 5(未完了): What’s Next for Mobile
