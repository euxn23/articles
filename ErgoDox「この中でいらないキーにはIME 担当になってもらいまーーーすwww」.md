この記事は[ErgoDox Advent Calendar 2016](http://qiita.com/advent-calendar/2016/ErgoDox)の9日目です。
ErgoDox でWin/Mac/Linux 共通して使えるIME の話をします。

# TL;DR
- 生ErgoDox だけでIME 制御やるのは無理！
- Win/Mac/Linux 共用させるには、どこかが痛みを伴う
- 結局 `変換` / `無変換` キーに設定して `AutoHotKey` なり `Seil` なり使うしかなさそう


# US 配列のIME 制御ってどうしてますか？
US 配列のキーボードの場合、JIS と違って `全角` / `半角` キーも、 `変換` / `無変換` キーもありません。Ctrl-Space 等でトグルするのが一般的かと思います。
しかしこれではIME の状態を意識しなくてはなりません。
MacOS のJIS にはSpace の両隣に `かな` / `英数`で、それぞれ全角、半角への状態変更が行えます。Windows でも同様にIME の割り当てを行っている人もいるかと思います。

このギミックをUS キーボードで実装するためにKarabiner を使ってCmd やOpt を変換/無変換に当てるというのを見ます。
しかしここについてはMacOS の話ばかり多く、Windows/Linux での話は少ないですし、実際に手段も容易ではないです。(というか、Karabiner がすごすぎる……)


# ErgoDox のキーボード側にIME 変更ボタンをマッピングすれば解決するのでは？
ErgoDox は分離型であることに加え、 `qmk firmware` によってキーを自由にマッピングできることも大きな特徴です。
しかし、qmk firmware には、というかUS 配列には直接全角/半角を制御するキーはありません。
ですが、何かしらのキーをOS 側でIME 制御に置き換えることで実現が可能です。

- Windows: IME ON/OFF にOS が認識可能な特殊キーを割り当てることが可能(IME 設定から可能/外部ソフトウェア不要)
- Mac: Karabiner で設定(簡単)
- Linux: mozc のON/OFF にOS が認識可能な特殊キーを割り当てることが可能(mozc 以外不要)

なので、普段使わない `F15` / `F16` にIME になってもらいました。

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr"><a href="https://twitter.com/mizchi">@mizchi</a> ErgoDox側でGUI/任意のキーに当てて、任意のキーをkarabinerでIME制御に変更する方法があります(任意のキーは死ぬ)</p>&mdash; yutaszk (@euta23) <a href="https://twitter.com/euta23/status/745540343710392320">2016年6月22日</a></blockquote>

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">ErgoDoxのIMEはF15とF16にお亡くなりになってもらうべしって話をどこかで書く</p>&mdash; yutaszk (@euta23) <a href="https://twitter.com/euta23/status/746005350571118593">2016年6月23日</a></blockquote>

しかしこれでは問題が発生しました。

- Windows のターミナル上で `F15` / `F16` を空打ちすると `~` が入力されてしまう。
- Mac でたまに事故って画面の明るさが変わる
- Linux で `F15` / `F16` が認識されない

つらい。


# 各OS に共通して認識されるが要らないキーを探す

まずすべてのOS に共通する特殊キーを検討します。

- Media: OS の認識ではない
- Pause/Break: 要らない気がしたけどOS によるっぽい
- PrtScr: 要らないがOS による
- HOME/END: まあなくてもがんばれる気がする(vim)
- Capslock: 要らない気がするが既にでCtrl に変えてる人多そう
- INSERT: 要らない気がする
- HELP: 存在を知らなかったので要らないと思う
- SELECT: 存在を知らなかったので(ry

以上から、HOME/END、INSERT/HELP/SELECT で検討してみました。

- HOME/END: 全OS で動作するが空打ちでHOME/END が動くのを無効化できなかった
- INSERT: 動作するが空打ちで誤爆する、Windows で割り当て不可
- HELP: Linux で認識不可
- SELECT: Linux で認識不可

むずかしい。


# 結論: `変換` / `無変換` に当てる

結局無理だったので、US キーボードに本来存在しない `変換` / `無変換` に割り当てます。
qmk firmware 上では `KC_HENK` と `KC_MHEN` がそれぞれ `変換` / `無変換` 相当のキーになります。かっこいい。
ただし本来存在しないので、このキーの認識は `AutoHotKey` / `Karabiner(Seil)` に頼ります。

- Windows: `AutoHotKey` を使用して `0x079` を `変換` に、 `0x07B` を `無変換` に割り当てる。
    - ビルド済バイナリは[yutaszk/ahk-henk-mhen v1.0.0](https://github.com/yutaszk/ahk-henk-mhen)より
- Mac: `Karabiner` 拡張である `Seil` を使用し、設定の `For Japanese` 項目の `Enable NFER/XFER Key on PC keyboard` を有効にする。
- Linux: mozc のON/OFF に普通に設定できる。

AHK がビルド済のものを使えるのがせめてもの救いという感じで、IME を切り替えるということの難しさを感じました。


明日は@miyaoka さんの「変わり種ErgoDox紹介」です！
