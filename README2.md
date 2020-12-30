# Webブラウザで図形をドラッグ＆ドロップで動かせるようにしてみよう！2週目<br><br>EP18116 村松直幸

[[1週目(前回)]](https://hackmd.io/cotccYjGQCi7Y_T_wJz8ow)

[2週目(今回)]

[[3週目(次回)]](https://hackmd.io/Med9J4N4R2ChMmR1JHdA5A)

[[4週目]](https://hackmd.io/be5H26EoT2uxEHkjoyRItA)

[[5週目]](https://hackmd.io/90fz3WRwRramMEDoCvgqAg)
<br>

**[オンラインエディタ(Try Elm!)](https://elm-lang.org/try)**

<br>

## 作業２週目(11月12~26日)


## Elmで大事なこと(メモ)

* プログラムを書くとき、「見た目」と「中身」をしっかり分離させて考えること！
* view(見た目)は短命、model(中身)は長寿である。
  viewはmodelの呼び出し、参照を密に行う。
  その逆は、通知(イベント)程度にし、viewとmodelゆるく結合させることが大事である!
* Elmは、modelの状態を記憶する。viewの状態は記憶しない！
* Elmは、modelという１か所に状態を記憶させる。ここがElmの優秀な点(The Elm Architecture)である！
* カウンタプログラムの例が、The Elm Architectureの特徴を全て抑えている！

などなど

## The Elm Architectureとは
[The Elm Architecture](https://guide.elm-lang.jp/architecture/)は、ウェブアプリケーションやゲームのような、対話的なプログラムを設計するためのパターンである。

Elmが画面に表示するためのHTMLを出力し、コンピュータは画面の中で起きたこと、例えば「ボタンがクリックされたよ！」というようなメッセージをElmへ送り返す。

このとき、プログラムの中では何が起きているのだろう？ Elmでは、プログラムは必ず3つのパーツに分解できる。

* Model — アプリケーションの状態
* View — 状態を HTML に変換する方法
* Update — メッセージを使って状態を更新する方法

この3つのコンセプトこそ、The Elm Architecture の核心である。

The Elm Architectureの本質が分かりやすく説明されていたため、載せておく。
<br>
　<img src="https://i.imgur.com/EmqTdFI.png" width="500">
<br>

## Elmを試して学ぶ（続き）
**[オンラインエディタ(Try Elm!)](https://elm-lang.org/try)**
<br>　<img src="https://i.imgur.com/qjRe0pt.png" width="600"><br>
 
[ここの　Here　を押すと色んなサンプルコードを試せるサイトを開ける。](https://elm-lang.org/examples)（気づきませんでした）

　<br><img src="https://i.imgur.com/2iTFOLo.png" width="600"><br>
<br>

　また、一部のプログラム (例: import )は、カーソルを合わせるとHintが表示される。
　<br><img src="https://i.imgur.com/QZ35wtb.png" width="500"><br>
 
 Hintをクリックすると、解説ページが表示される。(本来は英語のため、翻訳が必要)
　<br><img src="https://i.imgur.com/yNW9l8N.png" width="500"><br>
<br>

### Playgraundでできること(メモ)
https://package.elm-lang.org/packages/evancz/elm-playground/latest/Playground#game

<br>

### 画像を表示させるサンプルコード
　<br><img src="https://i.imgur.com/7bNALIc.png" width="500"><br>
 

```ruby
import Playground exposing (..)

main =
  picture
    [ image 100 100 "https://i.imgur.com/H8VLMhf.png"
      --横幅、高さ、表示させたい画像のURL　という順番
    ]
```

<br>

### 木の図形を表示させるサンプルコード

　<br><img src="https://i.imgur.com/cSnxNKJ.png" width="500"><br>

```ruby
import Playground exposing (..)

main =
  picture
  --picture タイプを指定
      [ rectangle brown 40 200
      --rectangle で四角形、brownで茶色を指定、40は横幅、200は縦幅
        |> moveDown 100
        --moveDownで図形の位置を下側に調整、100は距離
    , circle green 100
    --circle で円を作成、greenで緑を指定、100は円の大きさ
        |> moveUp 100
        --moveUp で図形の位置を上側に調整、100は距離
    ]
```

<br>

## マウスを合わせたところに円を表示させるサンプルコード
　<br><img src="https://i.imgur.com/gFpvo8U.png" width="500"><br>

```ruby
import Playground exposing (..)

main =
  game view update ()

view computer memory =
--view:  画面に表示する図(見た目)の部分
--memoryは変数名
  [ circle lightBlue 30
  -- 円を、ライトブルー色で大きさは30で表示
      |> moveX computer.mouse.x
      --マウスのx座標に移動させる
      |> moveY computer.mouse.y
      --マウスのy座標に移動させる
      |> fade (if computer.mouse.down then 0.2 else 1)
      --もし、マウスをドラッグしているなら濃さを0.2、それ以外のときは濃さを1に
  ]


update computer memory =
--メモリ更新の部分(１秒間に60回の更新)
  memory
  --memory を更新させる
```

このプログラムは今回の目的のプログラムに近いものである気がするので、これを編集して利用してみる。

図をドラッグ＆ドロップで動かすには、マウスダウン時にその位置に図形があるか調べ、あればその図形と図形の原点からのオフセットを記憶、マウス移動時はマウスの位置からオフセット分を引いた位置を図形の位置としてやればドラッグできます。らしい(メモ)
<br>

### 創成Dの記憶を思い出す
　下の画像は、物体Aと物体Bの座標の差を計算し、範囲内に収まっているかを判定するプログラム。
 これを参考にする。
<br> <img src="https://i.imgur.com/djGESn1.png" width="500"><br>
 
もし、<br>
**マウスがドラッグされた**
かつ<br>
**[(円のx座標-マウスのx座標)の２乗 + (円のy座標-マウスのy座標)の２乗]の平方根 < 円の半径**
となっていれば、上手く行くのでは？
<br>

### やってみる

```ruby
if computer.mouse.down && 
   sqrt((cir.x - computer.mouse.x)*(cir.x - computer.mouse.x) + (cir.y - computer.mouse.y)*(cir.y - computer.mouse.y)) < 30/2
   --computer.mouse.down　はマウスが押されたとき
   --sqrt... は座標を計算し、円の半径(30/2)にあるかどうか
```

<br>

また、viewの部分も、図形が常にマウスの位置に移動するのではなく、ドラッグするまでは動かないように変更する必要がある。

それらを踏まえ、他のサンプルコードを参考にしながら一旦書いてみた。
**ソースコード**　(未完)
```ruby
import Playground exposing (..)

main =
  game view update ()
    { x = 0
    , y = 0
    --事前にxとyを設定し、値は0(真ん中)
    }


view computer cir =
--view:  画面に表示する図(見た目)の部分
--cirは変数名
  [ circle lightBlue 30
  -- 円を、ライトブルー色で大きさは30で表示
      |> moveX cir.x
      --x座標を設定
      |> moveY cir.y
      --y座標を設定
      |> fade (if computer.mouse.down then 0.2 else 1)
      --もし、マウスをドラッグしているなら濃さを0.2、それ以外のときは濃さを1に
  ]


update computer cir =
--メモリ更新の部分(１秒間に60回の更新)
  [ if computer.mouse.down && 
       sqrt((cir.x - computer.mouse.x)*(cir.x - computer.mouse.x) + (cir.y - computer.mouse.y)*(cir.y - computer.mouse.y)) < 30/2
    then
        x = computer.mouse.x
        y = computer.mouse.y
        --条件を満たしていたら、x, yをマウスの座標の値に
    else
        x = cir.x
        y = cir.y
        --条件を満たしていなかったら、x, yはそのまま
  ]        
```

<br>

案の定エラーが発生。
　<br><img src="https://i.imgur.com/DgAV02y.png" width="500"><br>
<br>

エラー文を翻訳してもよく分からない…
 <br><img src="https://i.imgur.com/3dukmmd.png" width="500"><br>
<br>

## 次回(３週目)の目標
* 情報を集め、エラーを解決させる。
* gameを使うよりも、sandbox(html版)を利用した方がいいと聞いたので、そちらを調べてみる。

[[次回(３週目)]](https://hackmd.io/Med9J4N4R2ChMmR1JHdA5A)
