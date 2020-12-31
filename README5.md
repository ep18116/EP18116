# Webブラウザで図形をドラッグ＆ドロップで動かせるようにしてみよう！5週目（最終回）<br><br>EP18116 村松直幸



[[1週目]](https://hackmd.io/cotccYjGQCi7Y_T_wJz8ow)

[[2週目]](https://hackmd.io/ea7tOUt5Q_mTh3Bo1WN7dg)

[[3週目]](https://hackmd.io/Med9J4N4R2ChMmR1JHdA5A)

[[4週目(前回)]](https://hackmd.io/be5H26EoT2uxEHkjoyRItA)

[5週目(今回)]

**[オンラインエディタ(Try Elm!)](https://elm-lang.org/try)**
<br>
<br>

## 解決すべき問題

前回で挙げた未解決の問題を確認する。

* **ドラッグ＆ドロップにより図形の座標を動かす**
* **図をドラッグしたら、カーソルがその位置のまま図形を動かせるようにする(offset)**
* **ソースコードを50行以内に収める**

今回でこれら全てを解決する。
<br>
<br>

## **ドラッグ＆ドロップにより図形の座標を動かす**

方法として、**フラグ(flag)** を用いる方法を考える。

順序としては、

1. **Modelにflagの情報を加える**
2. **flagの初期値をFalseに設定する**
3. **ドラッグした時、flagをTrueにする**
4. **ドロップした時、flagをFalseにする**
5. **マウスを動かしたときにTrueであったら、xとyの値を更新する**

これらを満たすことができれば目的を達成できると考えられるので、実践してみる。
<br>

1. **Modelにflagの情報を加える**
```ruby
type alias Model = { x: Int , y: Int, color: String, flag: Bool }
```

2. **flagの初期値をFalseに設定する**
```ruby
init : Model
init = { x = 0 , y = 0 , color = "skyblue", flag = False}
```

3. **ドラッグした時、flagをTrueにする**
```ruby
    Drag ->
      {model | color = "lightblue" , flag = True}
```

4. **ドロップした時、flagをFalseにする**
```ruby
    Drop ->
      {model | color = "skyblue", flag = False}
```

5. **マウスを動かしたときにTrueであったら、xとyの値を更新する**

ここで、このように書いたらエラーが発生した。
```ruby
  case msg of
    Move x y ->
      if model.flag == True then 
          {model | x = x - 25, y = y - 25}
```

elmでは、**if文を使った場合、elseも必ず使う必要がある**。そのため、「elseの場合はそのまま」という文を追加する必要がある。<br>
(また、もしTrueだったら、という文を書くためにTrueと書く必要はないため、そこも省略する)

```ruby
update msg model =
  case msg of
    Move x y ->
      if model.flag then 
          {model | x = x - 25, y = y - 25}
      else model 
```

このように書いた結果、エラーが発生せず無事成功した。

<br>![](https://i.imgur.com/2zS02Lm.png)
<br>


(当初、図形を動かすためには、[もし図形内の座標でDragしたなら...]という書き方で書く必要があると考えていた。<br>
しかし、今回は青色の四角形を作成している文の中でDragメッセージを呼び出したことにより、この[青色の四角形の図形をDragしたら...]という書き方が自然にできていた。そのため、第２週目で考えていたような座標の計算は特に必要なかった。)
<br>

**ソースコード(全文)**
```ruby
--ドラッグ＆ドロップにより図形の座標を動かす
import Browser
import Html exposing (Html, div, span, text)
import Html.Events exposing (..)
import Html.Attributes exposing (style)
import Json.Decode exposing (map2, field, int)

main =
  Browser.sandbox
    { init = init, update = update, view = view}


-- MODEL -
type alias Model = { x: Int , y: Int, color: String, flag: Bool }
--現在の座標を把握するためには、Int型のx, yの値が必要。色とflagの情報も与える

init : Model
init = { x = 250 , y = 250 , color = "skyblue", flag = False}
--xとyの初期値は0で設定。初期の色はskyblue、flagはFalse


-- UPDATE --
type Msg = Move Int Int
         | Drag
         | Drop
--update関数はこのように定義されている３つのメッセージを受け取る可能性がある

update : Msg -> Model -> Model
update msg model =
  case msg of
    Move x y ->
      if model.flag then
      --もしflagがTrueだったのなら
          {model | x = x - 25, y = y - 25}
          --座標をマウスの位置に動かす
      else model
      --flagがFalseなら、何もしない

    Drag ->
      {model | color = "lightblue" , flag = True}
      --もしDragを受け取ったら、色をlightblueにし、flagをTrueにする
      
    Drop ->
      {model | color = "skyblue", flag = False}
       --もしDropを受け取ったら、色をskyblueにし、flagをFalseに


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


## 図をドラッグしたら、カーソルがその位置のまま図形を動かせるようにする(offset)

現在のままだと、**図形のどの位置をドラッグしても、図形を動かすと図形の真ん中にマウスが合わさってしまう**という状態になっている。

これは、**offset**を扱うことにより改善できるとの情報を得たので、offsetについて調べてみた。

今までは座標を手に入れるために**client**を用いていたが、座標を得る方法は複数ある。offsetはそのうちの１つである。こちらのサイトを読むと、それらが非常に分かりやすい。<br>
[マウスイベントで取得されるカーソル座標パラメータの整理(offset, page, screen, client)](https://qiita.com/yukiB/items/31a9e9e600dfb1f34f76)

サイト内では、offsetの説明は、

> 要素内でのカーソル座標(マウスが載っているDOMの左上を原点とした座標)を取得。

となっていた。

つまり、これを使えば、**図形をドラッグした場所の座標を手に入れることができる**。

しかし、このように図形の箇所にそのまま書いてもうまくない。

```el
        , on "mousemove" (map2 Move (field "offsetX" int) (field "offsetY" int))
```

offsetで得た座標をそのままに動かしてもおかしな挙動になってしまう。やりたいことは、**offsetで得た座標を基に、図形の位置を調整することである**。

そこで、方法として、

**図形の中でドラッグしていない間はoffsetで座標を覚えておき、ドラッグした時に、[図形の左上端の座標(x, y) - offsetで得た座標]となるように計算**　をするという方法が考えられる。
<br>

やるべきことを纏めると、

1. **Modelにoffsetで得た座標を覚えておく変数、adjustX,adjustYを追加する**
2. **初期値は何でもいいので0に設定する**
3. **メッセージにAdjust Int Intを追加する**
4. **図形の中でマウスを動かしている間、offsetで得た座標をメッセージとしてAdjustに送る**
6. **Adjustで、ドラッグしていない間はadjustX,adjustYにoffsetで得た情報を覚えておく**
7. **Moveで、座標がx-adjustX, y-adjustYになるように調整する**

これらを満たすことができれば、目的を達成できると考えられるので、実践してみる。

1. **Modelにoffsetで得た座標を覚えておく変数、adjustX,adjustYを追加する**

```ruby
type alias Model = { x: Int , y: Int, color: String, flag: Bool , adjustX: Int , adjustY: Int}
```

2. **初期値は何でもいいので0に設定する**
```ruby
init = { x = 250 , y = 250 , color = "skyblue", flag = False , adjustX = 0 , adjustY = 0}
```

3. **メッセージにAdjust Int Intを追加する**

```ruby
type Msg = Move Int Int
         | Drag
         | Drop
         | Adjust Int Int
```

4. **図形の中でマウスを動かしている間、offsetで得た座標をメッセージとしてAdjustに送る**
```ruby
        , on "mousemove" (map2 Adjust (field "offsetX" int) (field "offsetY" int))
```

5. **Adjustで、ドラッグしていない間はadjustX,adjustYにoffsetで得た情報を覚えておく**

```ruby
    Adjust x y ->
      if model.flag then model 
      else {model | adjustX = x , adjustY = y}
```

6. **Moveで、座標がx-adjustX, y-adjustYになるように調整する**

```ruby
    Move x y ->
      if model.flag then 
          {model | x = x - model.adjustX , y = y - model.adjustY }
      else model
```
<br>

実行してみた結果、確かにカーソルの位置のまま図形を動かすことができたが、図の中でマウスを動かしている間は図が動かないという動作になってしまった。

色々と試してみた結果、この箇所の書き方に問題があることが分かった。

```ruby
view : Model -> Html Msg
view model =
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
        , on "mousemove" (map2 Adjust (field "offsetX" int) (field "offsetY" int))
        --座標を与える
        --offsetとすると、図形の中の座標を返す。
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

良くは分からないが、ここの書き方を下のように調整し、必要のない座標を表示する箇所を消去してみた結果、無事座標内をマウスで動かしても図形が動くことが確認できた。

```ruby
view : Model -> Html Msg
view model =
  div [ style "background-color" "white"
        --背景を設定、色は白
        , style "height" "100vh"
        --背景の高さは100vh
        , on "mousemove" (map2 Move (field "clientX" int) (field "clientY" int))
        --座標を与える
    ]
    [ div
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
        , on "mousemove" (map2 Adjust (field "offsetX" int) (field "offsetY" int))
        --座標を与える
        --offsetとすると、図形の中の座標を返す。
        , onMouseDown Drag
        --ドラッグをしている間はDragメッセージを送る
        , onMouseUp Drop
        --ドロップしたら、Dropメッセージを送る
        ]
        []
    ]
```

<br>![](https://i.imgur.com/6wz9pck.png)<br>

**ソースコード(全文)**
```ruby
--図をドラッグしたら、カーソルがその位置のまま図形を動かせるようにする
import Browser
import Html exposing (Html, div, span, text)
import Html.Events exposing (..)
import Html.Attributes exposing (style)
import Json.Decode exposing (map2, field, int)

main = Browser.sandbox {init = init, update = update, view = view}


-- MODEL -
type alias Model = { x: Int , y: Int, color: String, flag: Bool , adjustX: Int , adjustY: Int}
--座標を得るためのx, yの値、色とflagの情報、offsetで得た座標を覚えるためのadjustX,Y


init : Model
init = { x = 250 , y = 250 , color = "skyblue", flag = False , adjustX = 0 , adjustY = 0}
--xとyの初期値は0で設定。初期の色はskyblue、flagはFalse


-- UPDATE --
type Msg = Move Int Int
         | Drag
         | Drop
         | Adjust Int Int
--update関数はこのように定義されている４つのメッセージを受け取る可能性がある

update : Msg -> Model -> Model
update msg model =
  case msg of
    Move x y ->
      if model.flag then 
      --もしflagがTrueだったのなら
          {model | x = x - model.adjustX , y = y - model.adjustY }
          --offsetで得た座標を引いて図形の座標を調整
      else model
      --flagがFalseなら、何もしない

    Drag ->
      {model | color = "lightblue" , flag = True}
      --もしDragを受け取ったら、色をlightblueにする
      
    Drop ->
      {model | color = "skyblue", flag = False}
       --もしDropを受け取ったら、色をskyblueにする
       
    Adjust x y ->
      if model.flag then model 
      --もしflagがTrueなら、何もしない
      else {model | adjustX = x , adjustY = y}
      --flagがFalseなら、その座標を覚えておく


-- VIEW --
view : Model -> Html Msg
view model =
  div [ style "background-color" "white"
        --背景を設定、色は白
        , style "height" "100vh"
        --背景の高さは100vh
        , on "mousemove" (map2 Move (field "clientX" int) (field "clientY" int))
        --座標を与える
    ]
    [ div
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
        , on "mousemove" (map2 Adjust (field "offsetX" int) (field "offsetY" int))
        --座標を与える
        --offsetとすると、図形の中の座標を返す。
        , onMouseDown Drag
        --ドラッグをしている間はDragメッセージを送る
        , onMouseUp Drop
        --ドロップしたら、Dropメッセージを送る
        ]
        []
    ]
```
<br>

## ソースコードを50行以内に収める

今回は、余計な改行やコメントを含めると、ソースコードが85行になってしまっている。また、ドラッグした時に図形の色が変わるというのは、本来の目的の中には含まれていない。そのため、それらを消去した簡略版を最後に載せておく。

```ruby =
import Browser
import Html exposing (Html, div, span, text)
import Html.Events exposing (..)
import Html.Attributes exposing (style)
import Json.Decode exposing (map2, field, int)

main = Browser.sandbox {init = init, update = update, view = view}

type alias Model = { x: Int , y: Int , adjustX: Int , adjustY: Int , flag: Bool}

init : Model
init = { x = 250 , y = 250 , flag = False , adjustX = 0 , adjustY = 0}

type Msg = Move Int Int | Drag | Drop | Adjust Int Int

update : Msg -> Model -> Model
update msg model =
  case msg of
    Move x y ->
      if model.flag then 
          {model | x = x - model.adjustX , y = y - model.adjustY }
      else model
    Drag ->
      {model | flag = True}
    Drop ->
      {model | flag = False}
    Adjust x y ->
      if model.flag then model 
      else {model | adjustX = x , adjustY = y}

view : Model -> Html Msg
view model =
  div [ style "background-color" "white"
        , style "height" "100vh"
        , on "mousemove" (map2 Move (field "clientX" int) (field "clientY" int))
    ]
    [ div
        [ style "background-color" "skyblue"
        , style "position" "absolute"
        , style "left" (String.fromInt model.x ++ "px")
        , style "top" (String.fromInt model.y ++ "px")
        , style "width" "50px"
        , style "height" "50px"
        , on "mousemove" (map2 Adjust (field "offsetX" int) (field "offsetY" int))
        , onMouseDown Drag
        , onMouseUp Drop
        ]
        []
    ]
```
<br>

49行となり、無事50行以内に収まった。
