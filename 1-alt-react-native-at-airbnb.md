> 2016年から私達はReact Nativeに対して大きな投資を続けてきました。
>
> あれから2年の歳月が経ち、今こそ私達が取り組んできた試みと次に何を見据えているかを皆さんと共有すべき時だと判断しました。

本稿は私達AirbnbのReact Nativeへの取り組みとモバイルの"次"についてまとめたシリーズの第一弾となります。

Airbnbがサービスを開始した10年前、スマートフォンはまだ普及し始めたばかりでした。それから幾ばくが経ち、世界を旅行する人が増えるに従ってスマートフォンは今や私達の生活を支える必要不可欠なツールとなりました。新たな旅の形を提供する会社として、世界に通用するアプリを提供する事はとても重要な事だと認識しています。携帯端末はしばしば人々にとって必要不可欠な存在であり、外出している際の唯一の連絡手段でもあります。

2008年に(Airbnbの最初のユーザーである)3人のゲストがRausch Streetに滞在してから、携帯端末からの(Airbnbへの)予約数は年間で数百万件にまで増加しました。我々のアプリを通してホストは提供している物件の一覧を管理する事ができ、旅人は新たな場所や体験を発見するインスピレーションを指先一本で得る事ができます。

(上記で説明した様な)増加していくモバイル端末でのユースケースに対応する為、また新たな体験の創出や既存機能の改善を図るために私達はNativeの開発チームを100人以上に拡大してきました。

### React Nativeへの賭け

ゲストとホスト双方がAirbnbで素晴らしい体験を得られるように、また優れた開発者体験を(社内のエンジニアに)もたらせるように私達は日々新たなテクノロジーを検証しています。2016年時点で、その内の一つがReact Nativeでした。当時、我々はモバイルが如何に我々のビジネスにとって重要なものになっていたかを認識していましたが、我々の目標を達成する為に十分な数の開発者を揃える事ができていませんでした。結果として我々は(Nativeで開発する以外の)代替案を検討するようになったのです。我々のWebサイトは主にReactで書かれており、Reactはとても効率的でAirbnb社内でもあまねく支持を得ているフレームワークです。これらの理由から、我々はReact Nativeをより多くの開発者に開発の間口を広げる為の、そしてクロスプラットフォーム開発でリリースをより高速に届ける為のツールとして見据えていました。

私達がReact Nativeについての調査を開始した時、当然そのリスクについても認識していました。私達は新しく、先の読めない実績がないプラットフォームを我々のコードベースに追加したのです。それは我々のコードベースを一つのものにするどころか、断片化するリスクを孕んだ決断でした。また、我々は同時にReact Nativeに投資をするのであれば(投資という行為自体を)しっかりとやっていきたいと思っていました。我々がReact Nativeを用いて実現したいゴールは以下のようなものです。

- 組織としてスピード感を持って動けるようにする事
- "Native"の品質を維持する事
- クロスプラットフォーム開発を実現する事(コードをiOSとAndroidで二回書かない事)
- 開発者の体験を向上させる事

### 我々の体験

2年に渡る試みはいつしか真摯な努力になっていました。私達はネットワーキングやABテスト、I18nなどのインフラストラクチャ bridgeを構築するだけでなくshared element transitionsやparallaxエフェクト、ジオフェンシングなど複雑なNativeの機能を実現する為の確固たるインテグレーションを実現してきました。

我々はAirbnbの沢山の重要な機能をReact Nativeを用いて立ち上げてきました。"Experiences"という全く新たなビジネスやレビューやギフトカードなど多くの機能はそのうちの一つです。これらの多くの機能はNativeエンジニアが不足している時代に(同時に)構築されたものです。

それぞれのチームが多種多様な体験をReact Nativeと共に積んできました。React Nativeは素晴らしいツールになりうるという事が証明された一方で、それは技術的そして組織的な挑戦を投げかける存在でもありました。

本シリーズでは私達の体験を余す所なくお伝えし、次に何に取り組んでいるかも同時にお伝えします。

[Part two](https://medium.com/airbnb-engineering/react-native-at-airbnb-the-technology-dafd0b43838)（[日本語訳](https://github.com/react-native-jp/react-native-at-airbnb-jp-translation/blob/master/2-react-native-at-airbnb-the-technology.md)）では、React Nativeで(技術的に)うまくいった所、うまくいかなかった所を振り返ります。

[Part three](https://medium.com/airbnb-engineering/building-a-cross-platform-mobile-team-3e1837b40a88)（[日本語訳](https://github.com/react-native-jp/react-native-at-airbnb-jp-translation/blob/master/3-building-a-cross-platform-mobile-team.md)）では、クロスプラットフォームなモバイルチームを作るに当たって取り組んだ組織的な挑戦を振り返ります。

[Part four](https://medium.com/airbnb-engineering/sunsetting-react-native-1868ba28e30a)（[日本語訳](https://github.com/react-native-jp/react-native-at-airbnb-jp-translation/blob/master/4-sunsetting-react-native.md)）では、我々とReact Nativeの現在の状況に焦点を当て、AirbnbにおけるReact Nativeがどうなるであろうかをお伝えします。

[Part five](https://medium.com/airbnb-engineering/whats-next-for-mobile-at-airbnb-5e71618576ab)（[日本語訳](https://github.com/react-native-jp/react-native-at-airbnb-jp-translation/blob/master/5-what%E2%80%99s-next-for-mobile-at-airbnb.md)）では、React Nativeから学んだ事とそれらをNative開発にどの様に役立てているかをお話します。
