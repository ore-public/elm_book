#Html

[Htmlライブラリ](http://package.elm-lang.org/packages/evancz/elm-html)の説明になります。

このライブラリを使うことでHtmlを作ることが出来ます。
Htmlライブラリは[virtual-dom](https://github.com/Matt-Esch/virtual-dom)をElm用にそのままラップしたものなので、仮想DOMという描画方法を自然に使うことが出来ます。


パッケージのインストールコマンド

```
elm-package install elm-lang/html --yes
```

使用例ハローワールド

```hs:Elm

import Html exposing (div,text)

main = div [] [text "Hello World"]
```

以下モジュールごとの説明になります。

##[Htmlモジュール](http://package.elm-lang.org/packages/evancz/elm-html/1.1.0/Html)でHTML作る。

基本になるモジュールで、Html型があります。

```hs

type alias Html = VirtualDom.Node
type alias Attribute = VirtualDom.Property

```

基本になるnode関数です。

```hs

node : String -> List Attribute -> List Html -> Html
node = VirtualDom.node
```

一引数目は、htmlタグの種類を文字列で指定、
二引数目で、属性と値を設定します。（Html.Attributesモジュールに作る関数があります。）
三引数目は、子のHtmlです。

このnodeタグをラップした関数が用意されています。
divタグを作るには、div関数を使います。

```hs
div : List Attribute -> List Html -> Html
div = node "div"
```

helloというhtmlを作るコードです。

```hs

import Html(div,text)

main = div [] [text "hello"] ---textは何もタグで囲まれていない文字列Html a を作る

```

結果のイメージになります。

```html:

<div>
    <div>heloo</div>
</div>
```

inputタグを作ってみます。

```hs
import Html(input)
import Html.Attributes(id)

main = input [id "Input"] [] ---id は id属性を作る関数
```

```html:HTML

<div>
  <input id="Input">
</div>
```
HTMLを作ることが出来ました。
ほかにも[いろんなタグを作る関数も揃ってます。](http://package.elm-lang.org/packages/evancz/elm-html/1.1.0/Html)




##[Html.Attributesモジュール](http://package.elm-lang.org/packages/evancz/elm-html/1.1.0/Html-Attributes)で属性を付ける。

Html.AttributesモジュールにあるAttribute型を作る関数で、Htmlに属性を付けます。
class属性、id属性をつけてみます。

```hs

import Html(div)
import Html.Attributes(class,id)

main : Html
main = div [class "test"  
           ,id "a"]
           []
```

```

<div>
    <div class="test" id="a"></div>
</div>
```

このようにclass,id属性をつけて、別途cssファイルを読みこめば、ちゃんとcssも適用されます。

今度はstyle属性をつけて、cssを直接domに適応してみます。

文字列を改行を設定するcssをつけてみます。

```hs

style : List (String, String) -> Attribute

```

```hs

import Html(div)
import Html.Attributes(style)

break = ("word-break","break-all")
spaceWrap = ("white-space","pre-wrap")
width = ("width","100px")
lineBreak = style [spaceWrap,break,width]

main = div [lineBreak] [text "helllllllloooooooooooo"]

```

メモ：文字列中の改行がそのまま適応されるpreタグというのもあります。
画面の枠にそって自動で収まるようにするには、flexというcssもあります。

[他にもいろんな属性が揃ってます。](http://package.elm-lang.org/packages/evancz/elm-html/1.1.0/Html-Attributes)

書くときの注意；

Htmlの規格通り一つのタグに２つ同じ属性をつけたりは出来ません。

```hs
a = style[("width","100px")]
g = style[("height","100px")]

main = div [width',height'][]

<div><div style="height: 100px;"></div></div>

main = div[on "keydown" ...
          ,on "keydown" ...] ---こっちだけになる

```

それらはHTMLにインライン展開される。なのでElm中のCSSはモジュール性がある。



##[Html.Events](http://package.elm-lang.org/packages/evancz/elm-html/1.1.0/Html-Events)モジュールのon関数

このモジュール内ある関数を使って、HtmlにイベントのAttributeを付けることが出来ます。

直接使わないですが、on関数の説明です。

```hs
on : String -> Json.Decoder msg -> Attribute msg
```

on関数を使ってイベントの付いたHTMLを作ることが出来ます。

一引数目は、イベントの種類の指定。(例えば"input"、"click"、"keydown"、、、)
二引数目に、Json.Decoderを入れ、eventオブジェクトをデコードして値を取り出します。デコーダはいくつか用意されています。
(例えば、`event.target.value`が欲しいなら`targetValue`、`event.KeyCode`なら`keyCode`)

###二引数目のJson.Decoder型

on関数は二引数目に`Json.Decoder msg`型を取ります。Jsonライブラリは、Json型のデータ構造を扱うライブラリで、ここにJsonデコーダを入れると、イベントオブジェクトから指定した値を取り出せます。
取り出した値は型構築子に渡されたます。


Json.Decode参考
http://package.elm-lang.org/packages/elm-lang/core/1.1.1/Json-Decode
http://uehaj.hatenablog.com/entry/2015/02/23/010955


###クリックイベント付けるには

onClick関数は、クリックイベントを付ける関数です。

```elm
onClick : msg -> Attribute msg
onClick msg =
  on "click" (Json.succeed msg)
```



##[Html.Lazy](http://package.elm-lang.org/packages/evancz/elm-html/1.1.0/Html-Lazy)
指定した値が変化した時だけ、その部分を描画するlazy関数があります。
この関数を使うことで、仮想DOMの力を発揮させて、効率の良い描画ができます。

```hs

lazy : (a -> Html) -> a -> Html
lazy = VirtualDom.lazy

```

一引数目にviewの関数、二引数目に関数の引数になる値を入れます。
値が変更した時だけviewを更新します。