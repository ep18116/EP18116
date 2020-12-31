# Webブラウザで図形をドラッグ＆ドロップで動かせるようにしてみよう！3週目<br><br>EP18116 村松直幸

[[1週目]](https://github.com/ep18116/EP18116/blob/main/README1.md)

[[2週目(前回)]](https://github.com/ep18116/EP18116/blob/main/README2.md)

[3週目(今回)]

[[4週目(次回)]](https://github.com/ep18116/EP18116/blob/main/README4.md)

[[5週目]](https://github.com/ep18116/EP18116/blob/main/README5.md)

**[オンラインエディタ(Try Elm!)](https://elm-lang.org/try)**
<br>

## まずは前回のエラーの解決

**エラーの原因はlet-in式が無いせい**だったため、見よう見まねで実装。ついでにsize変数を追加。
（ｘとyを別々に書く方法しかわからず、ソースコードが長くなってしまっている）

　<br><img src="https://i.imgur.com/B4agskU.png" width="500"><br>

```ruby
import Playground exposing (..)

main =
  game view update
    { x = 0
    , y = 0
    , size = 0
    }
    --事前にxとyを設定し、初期値を0に

view computer cir =
--view:  画面に表示する図(見た目)の部分
--cirは変数名
  [ circle lightBlue cir.size
  -- 円を、ライトブルー色で大きさは30で表示
      |> moveX cir.x
      --x座標を設定
      |> moveY cir.y
      --y座標を設定
      |> fade (if computer.mouse.down &&
                  sqrt((cir.x - computer.mouse.x)*(cir.x - computer.mouse.x) 
                  + (cir.y - computer.mouse.y)*(cir.y - computer.mouse.y)) < cir.size
               then 0.2 else 1)
      --もし、マウスをドラッグしており、それが範囲内なら濃さを0.2、それ以外のときは濃さを1に
  ]

update computer cir =
--メモリ更新の部分(１秒間に60回の更新)
  let 
    size = 50
    x = 
      if computer.mouse.down && 
         sqrt((cir.x - computer.mouse.x)*(cir.x - computer.mouse.x) 
         + (cir.y - computer.mouse.y)*(cir.y - computer.mouse.y)) < cir.size
      then
        computer.mouse.x
        --条件を満たしていたら、xをマウスの座標の値に
      else
        cir.x
        --条件を満たしていなかったら、xはそのまま
        
    y = 
      if computer.mouse.down && 
         sqrt((cir.x - computer.mouse.x)*(cir.x - computer.mouse.x) 
         + (cir.y - computer.mouse.y)*(cir.y - computer.mouse.y)) < cir.size
      then
        computer.mouse.y
        --条件を満たしていたら、yをマウスの座標の値に
      else
        cir.y
        --条件を満たしていなかったら、yはそのまま

  in
  { x = x
  , y = y
  , size = size
  }
```
<br>

取り敢えず動くようにはなったが、問題点も多い。しかしこれ以上このgameを使った書き方に時間を割けないため、一旦ここまでにする。
<br>

## sandboxを使った方式に書き換える

### どこから手を付けるか悩んだため、Elmの基礎をしっかり読む

[**[ボタン]**](https://guide.elm-lang.jp/architecture/buttons.html)
このページで、Elmの構造を理解する上で非常に重要であるカウンタのプログラムについて解説されていた。

ここを読んでカウンタを理解しつつ、どのように書き換えればsandboxの形にすることができるのか模索する。

カウンタのプログラムにコメントを加えて理解しやすくした。

```ruby
import Browser
import Html exposing (Html, button, div, text)
import Html.Events exposing (onClick)


-- MAIN --

main =
  Browser.sandbox { init = init, update = update, view = view }

--ここで、画面に何を表示するのかを記述する。
--initでアプリケーションを初期化、
--view関数ですべてを画面に表示、
--ユーザーからの入力をupdate関数に渡す。


-- MODEL --

--Elmでは、アプリケーションの状態を、
--プログラムが扱える形にするモデル化が非常に重要である。
type alias Model = Int
--現在のカウントを把握するためには、Int型の値が必要。

init : Model
init =
  0
--初期値は0で設定。


-- UPDATE --

--各種イベントで生成されたメッセージにより、
--Modelが各場面でどのように変化させるかを記述する。
type Msg
  = Increment | Decrement
--update関数はこのように定義されている２つのメッセージを
--受け取る可能性がある。

--メッセージを受信したとき、何をすべきか記述する。
update : Msg -> Model -> Model
update msg model =
  case msg of
    Increment ->
      model + 1
  --Incrementメッセージを受け取ったらモデルをインクリメント。

    Decrement ->
      model - 1
  --Decrementメッセージを受け取ったらモデルをデクリメント。




-- VIEW --

view : Model -> Html Msg
--Modelを引数として受け取り、Htmlを出力する。

view model =
  div []
  --divは特に意味を持たない一般的なコンテナ。
    [ button [ onClick Decrement ] [ text "-" ]
    --onClickというのは、クリックするとメッセージを生成するということ、
    --このマイナスボタンはDecrementメッセージを生成する、ということ。
    , div [] [ text (String.fromInt model) ]
    , button [ onClick Increment ] [ text "+" ]
    --このプラスボタンはIncrementメッセージを生成する、ということ。
    --生成されたメッセージは、update関数に行く。
    ]
```
<br>

### 重要な流れ
1. 何かメッセージを受け取る
2. updateに渡して実行
3. 新しいモデルを取得する
4. viewを呼び出して新しいモデルを画面にどう表示するか計算する
5. 画面に表示する

**これらを繰り返す！**

つまり、
view関数によって表示された画面でユーザーが何か操作を行うとメッセージが生成され、
それをupdate関数が受け取ってモデルを更新（update）し、
view関数がそのモデルを元に画面を表示（view）している。
<br>

## 実際に試してみる

取り敢えずエラーが発生。
<br>![](https://i.imgur.com/a962lgI.png)<br>

自分でメッセージを定義するときは大文字で始めなくてはいけなかった。

直してみたところ、エラーが大量に発生。

<br>![](https://i.imgur.com/vUOflDq.png)<br>

エラーの内容は使っている変数のほとんどが未定義であるということであった。
どうやらPlaygroundのみで使える変数の形をそのまま移してしまったためにエラーが発生したようだ。
(circle や lightBlue も使えない)

sandboxの形式に合うように書き換えたいが、やり方が分からないため他のサンプルを参考にする。<br>
[[positions]](https://elm-lang.org/examples/positions)

**現状(未完成)**
```ruby
import Browser
import Html exposing (..)
import Html.Attributes exposing (..)
import Html.Events exposing (..)
import Random


main =
  Browser.sandbox { init = init, update = update, view = view }


-- MODEL --

--Elmでは、アプリケーションの状態を、
--プログラムが扱える形にするモデル化が非常に重要である。
type alias Model =
  { x : Int
  , y : Int
  }
--現在の座標を把握するためには、Int型のx, yの値が必要。

init : Model
init =
  { x = 200 
  , y = 200
  }
--初期値は200で設定。


-- UPDATE --

type Msg
  = Cir_move (Int, Int) 
  | Cir_stay (Int, Int)
--update関数はこのように定義されている２つのメッセージを
--受け取る可能性がある。


update : Msg -> Model -> Model
update msg model =  
  case msg of
    Cir_move (x, y) ->
      ( x = x
      , y = y
      )
  --Cir_moveメッセージを受け取ったら円を動かす

    Cir_stay (x, y) ->
      Model x y
  --Cir_stayメッセージを受け取ったら円はそのまま
 
 
-- VIEW --
  
view : Model -> Html Msg
--Modelを引数として受け取り、Htmlを出力する。

view model =
  div []
  --divは特に意味を持たない一般的なコンテナ。
    [ circle lightBlue 30
    -- 円を、ライトブルー色で大きさは30で表示

    , if computer.mouse.down && 
        sqrt((cir.x - computer.mouse.x)*(cir.x - computer.mouse.x) 
        + (cir.y - computer.mouse.y)*(cir.y - computer.mouse.y)) < 30/2
      then
        Cir_move
        --条件を満たしていたら、cir_moveメッセージを生成
      else
        Cir_stay
        --条件を満たしていなかったらcir_stayメッセージを生成
    ]
```
<br>
色々書き換えてはみたが、エラーは減らず…
<br>

## 次回(４週目)の目標

友達に教えてもらったこのページを参考にしつつ、情報を集め、図形を動かすところまで行く。<br>
[[Elm で mouse のイベントを取得する覚書]](https://blog.emattsan.org/entry/2019/05/26/093114)

[[4週目(次回)]](https://github.com/ep18116/EP18116/blob/main/README4.mdA)
