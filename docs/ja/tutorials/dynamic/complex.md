---
sidebar_position: 2
---

# 複雑なアニメーション

[Polylux](https://polylux.dev/book/dynamic/syntax.html) が提供する構文を基に、Touyingでも `only`、`uncover`、`alternatives` を利用できます。


## マーク形式の関数

マーク形式の関数を使うと、アニメーションを簡単に記述できます。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
At subslide #touying-fn-wrapper-raw((self: none) => str(self.subslide)), we can

use #uncover("2-")[`#uncover` function] for reserving space,

use #only("2-")[`#only` function] for not reserving space,

#alternatives[call `#only` multiple times \u{2717}][use `#alternatives` function #sym.checkmark] for choosing one of the alternatives.
```

ただし、すべての場合に動作するとは限りません。たとえば `uncover` をコンテキスト式の内部に置くとエラーになります。


## マーク形式の関数内でのアニメーション

上の例では マーク形式の記述から `self` にアクセスするために `touying-fn-wrapper-raw` を使いました。コールバックは `(self: none) => ..` と書く必要があります。`(self) => ..` では *the argument `self` is positional* というエラーが発生します。

`touying-fn-wrapper` と異なり、`touying-fn-wrapper-raw` は周囲のpause領域から抜けず、位置引数を通常のスライド内容として解析します。このため内部で `#pause`、`#meanwhile`、touying-fn-wrapper系の関数（`#only`、`#uncover`、`#effect` など）がすべて動作し、別の `touying-fn-wrapper-raw` の内部にも入れ子にできます。`#alert` のように、この関数を基盤とする関数も同じ動作を継承します。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide[
  #alert[First #pause Second #uncover("3-")[Third]]

  #pause

  Fourth
]
```

このスライドには4つのサブスライドがあります。一方、`touying-fn-wrapper` は位置引数を解析せずにラップ先の関数へ渡します。そのため内部の `#pause` や入れ子の `#uncover` が未解決のメタデータマークとして渡され、*Unsupported mark* で失敗します。通常は `touying-fn-wrapper-raw` を使い、`touying-fn-wrapper` を選ぶのは `last-subslide` や `repetitions` が本当に必要な場合だけです。

:::note[テーマ作者向け]

この動作を利用するには、本文を*位置引数*として `touying-fn-wrapper-raw` に渡してください。`.with(..)` でラップ先の関数へ事前に組み込むとパーサーから見えなくなり、内部のアニメーション関数が失敗します。

```typst
// 本文はパーサーから見えないため、内部の #pause と #uncover はパニックを起こす。
#let my-block(title: none, it) = touying-fn-wrapper-raw(_my-block.with(title: title, it))

// 本文は通常のスライド内容として解析されるため、内部のアニメーションも動作する。
#let my-block(title: none, it) = touying-fn-wrapper-raw(_my-block.with(title: title), it)
```

:::


## コールバック形式の関数

前述のレイアウト関数の制約を回避するため、Touyingはコールバック関数を使って常に動作する `only`、`uncover`、`alternatives` を実装しています。まず次のように導入します。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide(repeat: 3, self => [
  #let (uncover, only, alternatives) = utils.methods(self)

  At subslide #self.subslide, we can

  use #uncover("2-")[`#uncover` function] for reserving space,

  use #only("2-")[`#only` function] for not reserving space,

  #alternatives[call `#only` multiple times \u{2717}][use `#alternatives` function #sym.checkmark] for choosing one of the alternatives.
])
```

ここでは内容ブロックではなく、`self` パラメーターを持つコールバック関数を渡しています。続いて、`only`、`uncover`、`alternatives` の各関数を `self` から次のように取り出します。

```typst
#let (uncover, only, alternatives) = utils.methods(self)
```

以降は取り出した関数を呼び出します。

整数型の `self.subslide` は現在のサブスライド番号を表します。`only`、`uncover`、`alternatives` は、`self.subslide` を参照して現在のサブスライドを判断します。

:::warning[注意]

ここでは3つのサブスライドを作るため、`repeat: 3` を手動で指定しています。Touyingは `only`、`uncover`、`alternatives` が表示すべきサブスライド数を、utils経由のバインディングを使用する場合には推論できないためです。

:::

## only

`only` は指定したサブスライドでだけ内容を表示します。それ以外では内容は完全に消え、領域も確保しません。つまり `#only(index, body)` の結果は `body` または `none` になります。

位置には整数または `"2-"`、`"2-3"` のような文字列を指定できます。詳しくは [Polylux](https://polylux.dev/book/dynamic/complex.html) を参照してください。

さらに、`auto`（`only` が現れた時点のサブスライド位置を使用する指定）と、同じ意味の文字列 `"h"`、その派生形 `"h-"`、`"-h"` も使用できます。
`"!"` による反転指定もできます。`"!h"` や `"!2-4"` と書くと、その位置を除くサブスライドが対象になります。通常の位置指定と異なり、反転はサブスライド数を増やしません。

ウェイポイントについては[ウェイポイント](./waypoints.md)を参照してください。

## uncover

`uncover` は指定したサブスライドでだけ内容を表示します。それ以外では `cover` 関数によって隠されますが、領域は保持されます。つまり `#uncover(index, body)` は `body` または `cover(body)` を返します。

位置指定には整数や `"2-"`、`"2-3"` のような文字列を使います。詳しくは [Polylux](https://polylux.dev/book/dynamic/complex.html) を参照してください。\
前述の `only` で説明したほかの指定方法も使用できます。

`#pause` も内部では `cover` 関数を使用しており、より簡潔な構文でほぼ同じ効果を実現します。

## alternatives

`alternatives` はサブスライドごとに異なる内容を切り替えて表示します。例：

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide(repeat: 3, self => [
  #let (uncover, only, alternatives) = utils.methods(self)

  #alternatives[Ann][Bob][Christopher]
  likes
  #alternatives[chocolate][strawberry][vanilla]
  ice cream.
])
```

`alternatives` は `only` や `uncover` と異なり、適切な幅と高さへ自動調整できます。実際には `alternatives` には `start: 2`、`repeat-last: true`、`position: center + horizon` などのほかのパラメーターもあります。詳しくは [Polylux](https://polylux.dev/book/dynamic/alternatives.html) を参照してください。
## animate

`only`、`uncover`、`alternatives` はそれぞれ1種類の処理をします。`#animate` は1つの内容に複数の効果を適用し、効果ごとに対象サブスライドを指定できます。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide[
  #animate(
    [The same words, three ways.],
    effects: (
      (effect: "cover", subslides: 1),
      (effect: (body, ..) => text(fill: blue, body), subslides: 2),
      (effect: (body, ..) => text(fill: red, strong(body)), subslides: "3-"),
    ),
  )
]
```

各効果は3つのキーを持つ辞書です。`effect` は処理内容、`subslides` は `uncover` と同様の適用タイミング、`priority` は優先順位を表します。`subslides` のデフォルトは `"1-"`、`priority` のデフォルトは `1` です。

### 2種類の効果

効果は組み合わせ方が異なる2種類に分類されます。

**配置効果**は内容を表示するかどうかを決め、`"cover"`、`"remove"`、`"show"`、`swap(..)` が使えます。各サブスライドには1つだけ適用され、重ねられません。**スタイル効果**は任意の関数で、すべて入れ子になって適用されます。

スタイル関数は `(body, ..)` を受け取って内容を返します。単純な `text.with(fill: red)` では動作しません。Touyingはサブスライドや設定に応じた条件分岐を可能にするため、名前付きの `self` も渡して呼び出すので、 `(body, ..) => text(fill: red, body)` と記述します。カバー用メソッドもスタイルとして直接使用でき、`utils.alpha-changing-cover` は有効な効果です。

### 優先順位の決定

各 `#animate` には暗黙に `"show"` が優先度 `0` で設定され、独自の配置効果はこれを上書きします。配置効果では最も高い優先度が採用され、同順位なら最後に書かれたものが有効です。スタイルの優先度は入れ子の順序を決め、低いものほど内側になります。同順位では最後に書いたものが内側になり、両者が設定するプロパティで優先されます。

両方に共通する原則は、**最後に記述した効果が優先される**ことです。配置効果ではその処理が採用され、スタイルでは最も内側になります。

### animate-hiddenとanimate-removed

`#animate-hidden(..)` と `#animate-removed(..)` は、`#animate` の効果の基底に `"cover"` または `"remove"` を優先度 `0` で設定する短縮形です。最初に内容を隠したり削除したりする場合に便利です。

### 領域

`"cover"` は領域を確保し、`"remove"` は確保しません。それぞれ `uncover` と `only` の動作に対応します。

`swap(replacement)` は別の内容と置き換え、レイアウトの再計算を許可します。`swap(replacement, stretch: true)` では代わりに置き換え後の内容も確保する領域に加えるため、`alternatives` のように全サブスライドで同じサイズを保てます。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide[
  #animate(
    [before],
    effects: ((effect: swap([after], stretch: true), subslides: "2-"),),
  )
]
```

## touying-render

`#touying-render(body, subslides: ..)` はどこに置いても、内容を選択したアニメーション段階で描画します。内容自体は通常どおりアニメーション化されますが、どの段階を表示するか選べます。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#let steps = [first #pause second #pause third]

== The Steps
#steps

== Just the Middle
#touying-render(steps, subslides: 2, base: 1)
```

`subslides` には `uncover` と同じ指定方法が使え、`"2-4"` の範囲や現在位置を示す `"h"` にも対応します。単一段階だけでなく範囲全体を順に表示できます。`base:` はカウントの起点を設定し、`start:` と `repeat-last:` は `alternatives` と同様に動作します。

独立した内容には `base:` を明示してください。`base: auto` は外側のスライドのアニメーションを起点とするため、`"h"` に適していますが、それ以外では通常推奨されません。

`subslides` のウェイポイントラベルは `touying-recall` と同じく、描画内容自身のウェイポイントを指します。外側のスライドのものを参照するには `use-outer-waypoints: true` を渡します。

## touying-recall

`touying-render` が変数内の内容を受け取るのに対し、`#touying-recall(<label>)` は文書内に既に存在する内容を再利用します。ラベルは `#pause` を含むブロックや `touying-reducer` など、独自のサブスライドを持つ対象に付ける必要があります。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
== The Construction
#box[the frame #pause and the inscribed circle]<fig>

== Later
At its first stage that was #touying-recall(<fig>, subslides: 1).
```

`subslides` で段階を選び、`base:` は前述と同じ意味です。これにより、通常は最終段階しか残らない[記事モード](../../integration/article-mode)でもアニメーション化した図を利用できます。

スライド全体を再利用するとラベルが重複するため、コピーには番号を付けます。`<intro>` の最初の再利用には `<intro-1>` と `<intro-1.wp>`、次には `<intro-2>` のようなラベルが付きます。元のスライドは `<intro>` のままです。呼び出しの後にラベルを付けた `#touying-recall(<intro>)<intro-again>` ではコピーに `<intro-again>` と `<intro-again.wp>` が付きます。ラベル付き内容を再利用するだけでは元のラベルは再生成されませんが、`#touying-recall(<fig>)<fig-again>` とすれば、図を `@fig-again` で参照できます。

## コールバック形式の関数

これらの関数には `utils` 内にコールバック形式の対応関数があります。サブスライド数を自動計算する関数を使えない場合や、使いたくない場合に利用してください。