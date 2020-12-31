# Webブラウザで図形をドラッグ＆ドロップで動かせるようにしてみよう！<br><br>EP18116 村松直幸


[1週目(今回)]

[[2週目(次回)]](https://hackmd.io/ea7tOUt5Q_mTh3Bo1WN7dg)

[[3週目]](https://hackmd.io/Med9J4N4R2ChMmR1JHdA5A)

[[4週目]](https://hackmd.io/be5H26EoT2uxEHkjoyRItA)

[[5週目]](https://hackmd.io/90fz3WRwRramMEDoCvgqAg)

<br>

## 使用しているPCの基本的な情報
**デバイスの仕様**<br>
デバイス名：　　　 LAPTOP-6CUTHCRM　<br>
プロセッサ：　　 　 Intel(R)Core(TM)i5-8250U CPU @ 1.60GHz 1.80GHz　<br>
実装メモリ(RAM):　 8.00GB(7.86GB 使用可能)　<br>
システムの種類： 　64ビットオペレーティングシステム、x64ベースプロセッサ　<br>

**Windowsの仕様**<br>
エディション：　　 Windows 10 Pro<br>
バージョン：　　　 1903<br>
OSビルド：　　　　18362.1082<br>
<br>

## 作業１週目(11月5~12日)

## Elm(エルム)とは
　<img src="https://i.imgur.com/X8zQ8ZF.png" width="200"><br>
　　　　Elmのロゴ

**Elm**は、ウェブブラウザベースのグラフィカルユーザインタフェースを宣言的に作成するためのドメイン固有プログラミング言語である。Elmは純粋関数型言語であり、ユーザビリティ・パフォーマンス・堅牢性を重視して開発されている。静的かつ強力な型検査によって「事実上一切の実行時例外が起こらない」ことを売りにしている。
[(Wikipediaより)](https://ja.wikipedia.org/wiki/Elm_(%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0%E8%A8%80%E8%AA%9E))

**ブラウザ上で動くアプリケーション開発向けの言語であり、主にウェブアプリケーションやウェブサイトのUI・UX開発などに利用されている。**
<br>

### Elmの言語としての特徴

* Elmは関数型言語である
* Elmは、Haskell(ハスケル)がベースになっている
* コンパイラに入るまでは大変…
* しかし、実行速度がとても速い！
* そして、コンパイラ時にエラー（バグ）が殆ど出ない！
* 学習曲線に非常に気を遣って作られているため、プログラムが非常に作りやすい、作ってて楽しい！
* 非常に見やすく分かりやすいデザインをしているため、関数型言語の入門としても非常に優秀！
*  Elmで書いたコードをコンパイルすると、HTML、Javascript、CSSが生成され、ブラウザ上で動作確認することができる！

などなど、とても便利で優れた特徴をいくつも持っている言語である！
<br>

### 関数型言語とは
ここで、関数型言語についても調べてみる。
(すでに計算論とプログラミング言語論の講義で扱っている**Scheme**も、関数型言語である。)
　 <br>
  <img src="https://i.imgur.com/NwnsYp8.png" width="500">

 
 この図でまとめられているように、今まででもよく使っていた言語は手続き型、Javaはオブジェクト指向と呼ばれ、今回扱うElmは関数型言語と区分されている。
 
 主な特徴としては、
 
* 変数および関数に参照透過性があり、副作用が抑制または完全に排除されている
* つまり、関数の内部は外部にあるデータを一切頼らず、またそれらを変更しない
* 副作用が完全に排除された純粋関数型では変数に値を代入するという考え方がなく、変数に値が束縛される
* 処理を細かく関数に分け、それらを組み合わせて利用するためパイプラインや高階関数、クロージャなどが使われる

などが挙げられる。

難しい単語も多くいまいちよく分からない点もあるが、**Elmはもっと実用的な以下のものを実現するための手段として、関数型の考え方を使っているだけである。**

* 実用上ランタイムエラーがでないし、nullもないし、undefinedが関数だなんて話はありえない
* とてもわかりやすい親切なエラーメッセージによってより素早くコードに機能を追加できる
* コードの規模が大きくなっても全体の設計が壊れることがありえない
* すべてのElmパッケージにおいて、決められたルールに則って自動的にバージョン番号が付与されている

「関数型言語という少し見慣れないパラダイムを使っているElmを学ぶには時間が少しかかりますが、このガイドを読むのに使うたった数時間の投資に十分見合うだけの価値をもたらしてくれます。」
とのこと。([なぜ関数型を採用しているか](https://guide.elm-lang.jp/)より)
<br>

### Elmについての細かいガイド（翻訳サイト）はこちら↓
https://guide.elm-lang.jp/<br>
Elmについて非常に細かく解説がされている。困ったらまずはこのページを頼りにしよう。

#### Elmのインストールは、こちらのページから可能である。
https://guide.elm-lang.jp/install.html<br>
elmコマンドがターミナルから利用できるようなる。

### インストールをしなくても、こちらのオンラインエディター(Try Elm!)で、Elmをお試しすることができる。
**[オンラインエディタ(Try Elm!)](https://elm-lang.org/try)**
　 <br><img src="https://i.imgur.com/BUvjQwy.png" width="600"><br>

とりあえずは、こちらを利用する。
<br>

## 試して学ぶ
[言語の基礎](https://guide.elm-lang.jp/core_language.html#%E5%80%A4)<br>
こちらを読み進めながら、オンラインエディタで実際に試してみる。

#### まずは、こちらに載っているソースコードをコンパイルしてみる。
[カウンターのサンプル](https://guide.elm-lang.jp/)

```ruby
import Browser
import Html exposing (Html, button, div, text)
import Html.Events exposing (onClick)

main =
  Browser.sandbox { init = 0, update = update, view = view }
  --sandbox関数　init=0 初期状態を0というモデルにして利用する　updateをupdate関数として、viewをview関数として利用する。

type Msg = Increment | Decrement

update msg model =
  case msg of
    Increment ->
      model + 1

    Decrement ->
      model - 1

view model =
  div []
    [ button [ onClick Decrement ] [ text "-" ]
    , div [] [ text (String.fromInt model) ]
    , button [ onClick Increment ] [ text "+" ]
    ]
    
```
<br>

　<br><img src="https://i.imgur.com/Btkb59W.png" width="600"><br>
 
 特にエラーは出ず、プラスを押すと数字が１増加し、マイナスを押すと数字が１減少するカウンターが作成された。
<br>

#### 次に、文字列(Hello!)を表示するプログラムを試してみようとした。

　<br><img src="https://i.imgur.com/BSsT6Ma.png" width="600"><br>
 
エラーが表示されたため、翻訳をした。
　<br><img src="https://i.imgur.com/oFUYdD9.png" width="600"><br>

greet~を入力すれば良いのかと思い、試してみた。
　<br><img src="https://i.imgur.com/Obd35o6.png" width="600"><br>

またエラーが発生したので翻訳。
　<br><img src="https://i.imgur.com/FrABbEe.png" width="600"><br>

import~を入力してみた。
　<br><img src="https://i.imgur.com/qAym7UD.png" width="600"><br>

なんとか Hello! と表示された。

初めて扱う言語であり、基礎的なことも分からないためHello!と表示させるだけでも時間がかかってしまった。
<br>

## 次回(2週目)の目標

Elmの基礎を学びつつ、図形を描画させたりする。

[[次回 (2週目)]](https://github.com/ep18116/EP18116/blob/main/README2.md)
