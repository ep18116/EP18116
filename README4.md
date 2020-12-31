# Webブラウザで図形をドラッグ＆ドロップで動かせるようにしてみよう！4週目<br><br>EP18116 村松直幸

[[1週目]](https://hackmd.io/cotccYjGQCi7Y_T_wJz8ow)

[[2週目]](https://hackmd.io/ea7tOUt5Q_mTh3Bo1WN7dg?both)

[[3週目(前回)]](https://hackmd.io/Med9J4N4R2ChMmR1JHdA5A)

[4週目(今回)]

[[5週目(次回)]](https://hackmd.io/90fz3WRwRramMEDoCvgqAg)

**[オンラインエディタ(Try Elm!)](https://elm-lang.org/try)**
<br>

## 解決すべき問題

* **図形(四角形)を表示する**
* **図形の位置を指定する**
* **ドラッグを認識する**
* **ドロップを認識する**
* **ドラッグ＆ドロップにより図形の座標を動かす**
* **図をドラッグしたら、カーソルがその位置のまま図形を動かせるようにする(offset)**
* **ソースコードを50行以内に収める**

まだまだ問題はいっぱいある。
一気にやろうとすると混乱するので、上から１つずつ解決することを目指す。
<br>

## 図形(四角形)を表示する

前回までは図形の表示すらできていなかった。
取り敢えず四角形を表示させるだけのプログラムを書いてみる。

 　<br><img src="https://i.imgur.com/bnqhiLl.png" width="500"><br>
  
**ソースコード**
```ruby
--図形(四角形)を表示
import Browser
import Html exposing (..)
import Html.Attributes exposing (..)

main = Browser.sandbox{init=init, view=view, update=update}


-- MODEL --
type alias Model = Int

init : Model
init = 0


-- UPDATE --
type Msg = Figure
--メッセ―ジは受け取っても特に何もしない

update : Msg -> Model -> Model
update msg model =
  model


-- VIEW --
view : Model -> Html Msg
--Modelを引数として受け取り、Htmlを出力する。
view model =
  div[]
  [ div
    [ style "background-color" "skyblue"
    --背景の色を水色で指定
    , style "width" "50px"
    --横幅は50px
    , style "height" "50px"
    --縦幅は50px
    ]
    []
  ]
```
<br>

 特に座標も指定していないため、表示画面の左上隅に水色の四角形が表示された。
 <br>

## 図形の位置を指定する

[[Elm で mouse のイベントを取得する覚書]](https://blog.emattsan.org/entry/2019/05/26/093114)
まずはこのサイトを参考にしマウスの座標を習得する。

このプログラムを参考にし、カーソルの位置に図形が表示されるようにしたい。

取り敢えず雑に入れてみる。

<br>![](https://i.imgur.com/GZYxwE7.png)<br>

図形は表示されるが、当然マウスの座標の位置に図形は動かない。

[[Positions]](https://elm-lang.org/examples/positions)を参考にして、以下の３行を図形を表示している箇所に追加したところ、動いた。(このサンプルのtopとleftは逆にする必要がある)
```ruby
        , style "position" "absolute"
        , style "left" (String.fromInt model.x ++ "px")
        --図形の左端の座標とマウスのx座標を合わせる
        , style "top" (String.fromInt model.y ++ "px")
        --図形の上端の座標とマウスのy座標を合わせる
```

<br>![](https://i.imgur.com/UykpNsB.png)<br>

このままでは図形の左上を軸として図形が動くため、
[図形の大きさ - 図形の大きさ/2]
と x, y の座標を設定することにより、図形の中心が軸になるようにしてみる。
```ruby
update msg model =
  case msg of
    Move x y -> {x = x-25, y = y-25}
```

無事、図形の中心が軸となって図形が動いた。

**ソースコード**
```ruby
--図形の位置を指定する
import Browser
import Html exposing (Html, div, span, text)
import Html.Events exposing (on)
import Html.Attributes exposing (style)
import Json.Decode exposing (map2, field, int)

main =
  Browser.sandbox
    { init = init, update = update, view = view}


-- MODEL -
type alias Model = { x: Int , y: Int }
--現在の座標を把握するためには、Int型のx, yの値が必要

init : Model
init = { x = 0 , y = 0 }
--xとyの初期値は0で設定。


-- UPDATE --
type Msg = Move Int Int
--update関数はこのように定義されている１つのメッセージを受け取る可能性がある。

update : Msg -> Model -> Model
update msg model =
  case msg of
    Move x y -> {x = x-25, y = y-25}
--もしMoveを受け取ったら、図形を上のように動かす


-- VIEW --
view : Model -> Html Msg
view model =
  div []
    [ span
        []
        [ text ("(" ++ (String.fromInt model.x) ++ ", " ++ String.fromInt model.y ++ ")") ]
        --現在のマウスの座標を左上に表示
    , div
        [ style "background-color" "skyblue"
        --背景を設定、色は水色を指定
        , style "position" "absolute"
        , style "left" (String.fromInt model.x ++ "px")
        --図形の左端の座標とマウスのx座標を合わせる
        , style "top" (String.fromInt model.y ++ "px")
        --図形の上端の座標とマウスのy座標を合わせる
        , style "width" "50px"
        --横幅は50px
        , style "height" "50px"
        --縦幅は50px
        , on "mousemove" (map2 Move (field "clientX" int) (field "clientY" int))
        --座標を与える
        --clientXとすると、表示している画面の中の座標を返す。
        ]
        []
    , div
        [ style "background-color" "white"
        --背景を設定、色は白
        , style "height" "100vh"
        --背景の高さは100vh
        , on "mousemove" (map2 Move (field "clientX" int) (field "clientY" int))
        --座標を与える
        ]
        []
    ]
```
<br>
<br>

ここで、図形(水色の四角形)と白色の背景の両方に
```ruby
on "mousemove" (map2 Move (field "clientX" int) (field "clientY" int))
```
を書いている。どちらかを消してしまいたいが、
* 図形の方を消してしまうと、図形の動きが少々カクつく。
* 背景の方を消してしまうと、マウスを速く動かして図形の外に外れた場合、図形がマウスについて来なくなる。

となってしまうため、同じ文だが両方に書く必要がある。(解決する方法があるかも)

<br>

## ドラッグを認識する

いきなり
[座標の中でドラッグしたなら～]
と書くのは大変なので、まずは座標を関係無しに

* **クリックしたなら図形を少し薄い色(lightblue)にする**
* **クリックしていない間は通常通りの図形(skyblue)を表示する**

を満たせるようなプログラムを目指す。

[[Positions]](https://elm-lang.org/examples/positions)を参考にすると、
```ruby
onClick メッセージ
```
という方法でクリックを認識しメッセージを送っていた。
onClickを使うためには、最初に
```ruby
import Html.Events exposing (..)
```
を加える必要がある。
<br>

取り敢えず書いてみる。
```ruby
update msg model =
  case msg of
    Move x y -> 
      {x = x-25, y = y-25}
--もしMoveを受け取ったら、図形を上のように動かす

    Click ->
      style "background-color" "lightblue"
--もしClickを受け取ったら、図形の色を変更する
```

当然こんな書き方ではエラーが発生する。
<br>![](https://i.imgur.com/tIYOLzX.png)<br>

Model ～～～
という書き方ならエラーは出なくなるかと思い、試しに
```ruby
    Click ->
      Model 100 100
```
と書いてみた。

そうすると、クリックをすると図形の座標が(x, y) = (100, 100)の位置に移動した。
<br>![](https://i.imgur.com/qhEqI57.png)<br>

最初の設定で、Modelの情報は
```ruby
-- MODEL -
type alias Model = { x: Int , y: Int }
--現在の座標を把握するためには、Int型のx, yの値が必要
```
のようにxの値とyの値を設定していたため、このように書けばエラーが発生しないことが分かった。
<br>

Modelには様々な情報を与えることができるため、Modelに色の情報を与える方法を考える。

まず、Modelが持つ情報に、**色の情報を追加**する。
```ruby
-- MODEL -
type alias Model = { x: Int , y: Int, color: String}
```
updateを以下のように書き換える。
```ruby
update msg model =
  case msg of
    Move x y-> 
      { x = x-25
      , y = y-25
      , color = model.color}
      --もしMoveを受け取ったら、図形を上のように動かす

    Click ->
      { x = model.x
      , y = model.y
      , color = "lightblue"}
      --もしClickを受け取ったら、色をlightblueにする
```
（ここで、Moveではcolor、Clickではxとyの値は変化させる必要はない。そのため、次のように書くと見やすく省略することができる。）
```ruby
  case msg of
    Move x y-> 
      {model | x = x-25, y = y-25}

    Click ->
      {model | color = "lightblue"}
```
図形の色を設定していた箇所を書き換える。
```ruby
        [ style "background-color" model.color
```
いろいろと試してみた結果、何とかエラーが発生しないようにできた。


<br>![](https://i.imgur.com/WQ6okff.png)<br>

分かりづらいが、クリックすると色が変化する。

**ソースコード**
```ruby
--クリックすると図形の色が変化する
import Browser
import Html exposing (Html, div, span, text)
import Html.Events exposing (on)
import Html.Attributes exposing (style)
import Json.Decode exposing (map2, field, int)
import Html.Events exposing (..)

main =
  Browser.sandbox
    { init = init, update = update, view = view}


-- MODEL -
type alias Model = { x: Int , y: Int, color: String}
--現在の座標を把握するためには、Int型のx, yの値が必要

init : Model
init = { x = 0 , y = 0 , color = "skyblue"}
--xとyの初期値は0で設定。色はskyblue


-- UPDATE --
type Msg = Move Int Int
         | Click
--update関数はこのように定義されている２つのメッセージを受け取る可能性がある。

update : Msg -> Model -> Model
update msg model =
  case msg of
    Move x y-> 
      {model | x = x-25, y = y-25}
      --もしMoveを受け取ったら、図形を上のように動かす

    Click ->
      {model | color = "lightblue"}
      --もしClickを受け取ったら、色をlightblueにする


-- VIEW --
view : Model -> Html Msg
view model =
  div []
    [ span
        []
        [ text ("(" ++ (String.fromInt model.x) ++ ", " ++ String.fromInt model.y ++ ")") ]
        --現在のマウスの座標を左上に表示
    , div
        [ style "background-color" model.color
        --背景を設定、modelの色
        , style "position" "absolute"
        , style "left" (String.fromInt model.x ++ "px")
        --図形の左端の座標とマウスのx座標を合わせる
        , style "top" (String.fromInt model.y ++ "px")
        --図形の上端の座標とマウスのy座標を合わせる
        , style "width" "50px"
        --横幅は50px
        , style "height" "50px"
        --縦幅は50px
        , on "mousemove" (map2 Move (field "clientX" int) (field "clientY" int))
        --座標を与える
        --clientXとすると、表示している画面の中の座標を返す。
        , onClick Click
        --クリックしたらClickメッセージを送る
        ]
        []
    , div
        [ style "background-color" "white"
        --背景を設定、色は白
        , style "height" "100vh"
        --背景の高さは100vh
        , on "mousemove" (map2 Move (field "clientX" int) (field "clientY" int))
        --座標を与える
        ]
        []
    ]
```
<br>

### ドラッグ(onMouseDown)に書き換える

目的はクリックではなくドラッグしたらなので、onClickをonMouseDownに書き換える。

このままでは、一度ドラッグをして色を変化させた後は、元に戻ることはない。そのため、ドロップをしたら元の色に戻るようにしたい。
<br>

## ドロップを認識する

ドロップは
**onMouseUp**
で与えることができる。
ドロップしたら元の色に戻るようにしたいため、
onMouseDownと殆ど同じような書き方で実装してみる。

<br>![](https://i.imgur.com/WZxXDKU.png)<br>
<br>

特に問題なく、

* **ドラッグしている間は図形をlightblue色に**
* **ドロップしたら図形をskyblue色に**

を実装することができた。

**ソースコード**
```ruby
--ドラッグすると図形の色が変化する。ドロップすると元に戻る
import Browser
import Html exposing (Html, div, span, text)
import Html.Events exposing (..)
import Html.Attributes exposing (style)
import Json.Decode exposing (map2, field, int)


main =
  Browser.sandbox
    { init = init, update = update, view = view}


-- MODEL -
type alias Model = { x: Int , y: Int, color: String}
--現在の座標を把握するためには、Int型のx, yの値が必要。色の情報も与える

init : Model
init = { x = 0 , y = 0 , color = "skyblue"}
--xとyの初期値は0で設定。初期の色はskyblue


-- UPDATE --
type Msg = Move Int Int
         | Drag
         | Drop
--update関数はこのように定義されている３つのメッセージを受け取る可能性がある

update : Msg -> Model -> Model
update msg model =
  case msg of
    Move x y-> 
      {model | x = x-25, y = y-25}
      --もしMoveを受け取ったら、図形を上のように動かす

    Drag ->
      {model | color = "lightblue"}
      --もしDragを受け取ったら、色をlightblueにする
      
    Drop ->
      {model | color = "skyblue"}
       --もしDropを受け取ったら、色をskyblueにする


-- VIEW --
view : Model -> Html Msg
view model =
  div []
    [ span
        []
        [ text ("(" ++ (String.fromInt model.x) ++ ", " ++ String.fromInt model.y ++ ")") ]
        --現在のマウスの座標を左上に表示
    , div
        [ style "background-color" model.color
        --背景を設定、modelの色
        , style "position" "absolute"
        , style "left" (String.fromInt model.x ++ "px")
        --図形の左端の座標とマウスのx座標を合わせる
        , style "top" (String.fromInt model.y ++ "px")
        --図形の上端の座標とマウスのy座標を合わせる
        , style "width" "50px"
        --横幅は50px
        , style "height" "50px"
        --縦幅は50px
        , on "mousemove" (map2 Move (field "clientX" int) (field "clientY" int))
        --座標を与える
        --clientXとすると、表示している画面の中の座標を返す。
        , onMouseDown Drag
        --ドラッグをしている間はDragメッセージを送る
        , onMouseUp Drop
        --ドロップしたら、Dropメッセージを送る
        ]
        []
    , div
        [ style "background-color" "white"
        --背景を設定、色は白
        , style "height" "100vh"
        --背景の高さは100vh
        , on "mousemove" (map2 Move (field "clientX" int) (field "clientY" int))
        --座標を与える
        ]
        []
    ]
```
<br>

## 次回(５週目)の目標
* **ドラッグ＆ドロップにより図形の座標を動かす**
* **図をドラッグしたら、カーソルがその位置のまま図形を動かせるようにする(offset)**
* **ソースコードを50行以内に収める**

まだまだこれらの問題を解決できていないため、次回で解決する。

[[5週目(次回)]](https://hackmd.io/90fz3WRwRramMEDoCvgqAg)
