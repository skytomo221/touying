---
sidebar_position: 2
---

# はじめに

作業を始める前に、Typstを使用できる環境を用意してください。未導入の場合は、[Web App](https://typst.app/) を利用するか、LSP対応エディターに [Tinymist LSP](https://github.com/Myriad-Dreamin/tinymist) を導入できます。Tinymistには [VS Code拡張機能](https://marketplace.visualstudio.com/items?itemName=myriad-dreamin.tinymist)もあります。

Touyingを使うには、文書に次のコードを書くだけです。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(aspect-ratio: "16-9")

= タイトル

== 最初のスライド

こんにちは、Touying！

#pause

こんにちは、Typst！
```

これで最初のTouyingスライドが完成しました。

**ヒント:** `#import "config.typ": *` や `#include "content.typ"` などのTypstの構文を使えば、Touyingの文書を複数ファイルに分割できます。

## より高度な例

Touyingにはさまざまなスライドの記述方法があります。`#slide[..]` 構文を使うと、Touyingのより高度な機能を利用できます。

Touyingには美しいスライドを簡単に作成できる組み込みテーマが多数あります。たとえば、次の例を見てください。

```example
#import "@preview/touying:0.8.0": *
#import themes.university: *
#import "@preview/cetz:0.5.2"
#import "@preview/fletcher:0.5.8" as fletcher: node, edge
#import "@preview/numbly:0.1.0": numbly
#import "@preview/theorion:0.6.0": *
#import cosmos.clouds: *
#show: show-theorion

// Touying向けFletcherのバインディング
#let fletcher-diagram = touying-reduce.with(fletcher)

// 明示的なバインディングは不要で、次のように直接記述できます： `touying-(diagram|reduce)(cetz, {...})`
//#let cetz-canvas = touying-reducer.with(reduce: cetz.canvas, cover: cetz.draw.hide.with(bounds: true))
#let cetz-canvas = touying-reduce.with(cetz)

#show: university-theme.with(
  aspect-ratio: "16-9",
  // align: horizon,
  // config-common(handout: true),
  config-common(frozen-counters: (theorem-counter,)),  // アニメーション中は定理カウンターを固定
  config-info(
    title: [タイトル],
    subtitle: [サブタイトル],
    author: [著者],
    date: datetime.today(),
    institution: [所属],
    contact: [contact\@mail.com],
    logo: emoji.school,
  ),
)

#set heading(numbering: numbly("{1}.", default: "1.1"))

#title-slide()

== 目次 <touying:hidden>

#components.adaptive-columns(outline(title: none, indent: 1em))

= アニメーション

== シンプルなアニメーション

`#pause` を使うと、#pause 内容を後から表示できます。

#pause

Just like this.

#meanwhile

また、#pause `#meanwhile` を使うと、#pause 別の内容を同時に切り替えられます。

#speaker-note[
  + これは発表者ノートです。
  + `config-common(show-notes-on-second-screen: right)` を使わない限り表示されません。
]


== 複雑なアニメーション

サブスライド #touying-fn-wrapper-raw((self: none) => str(self.subslide)) では、

#uncover("2-")[`#uncover` 関数]で表示前の領域を確保でき、

#only("2-")[`#only` 関数]で領域を確保せずに切り替えられ、

#alternatives[`#only` を何度も呼ぶ \u{2717}][`#alternatives` を使う #sym.checkmark]ことで候補を切り替えられます。


== コールバック形式のアニメーション

#slide(
  repeat: 3,
  self => [
    #let (uncover, only, alternatives) = utils.methods(self)

    サブスライド #self.subslide では、

    #uncover("2-")[`#uncover` 関数]で表示前の領域を確保でき、

    #only("2-")[`#only` 関数]で領域を確保せずに切り替えられ、

    #alternatives[`#only` を何度も呼ぶ \u{2717}][`#alternatives` を使う #sym.checkmark]ことで候補を切り替えられます。
  ],
)


== 数式のアニメーション

`pause` を使った数式：

$
  f(x) &= pause x^2 + 2x + 1 \
  &= pause (x + 1)^2 \
$

#meanwhile

ここで #pause $f(x)$ の式が得られます。

#pause

因数分解すると次の結果が得られます。


== CeTZのアニメーション

TouyingでのCeTZアニメーション：

#cetz-canvas({
  import cetz.draw: *

  rect((0, 0), (5, 5))

  (pause,)

  rect((0, 0), (1, 1))
  rect((1, 1), (2, 2))
  rect((2, 2), (3, 3))

  (pause,)

  line((0, 0), (2.5, 2.5), name: "line")
})


== Fletcherのアニメーション

TouyingでのFletcherアニメーション：

#fletcher-diagram(
  node-stroke: .1em,
  node-fill: gradient.radial(blue.lighten(80%), blue, center: (30%, 20%), radius: 80%),
  spacing: 4em,
  edge((-1, 0), "r", "-|>", `open(path)`, label-pos: 0, label-side: center),
  node((0, 0), `reading`, radius: 2em),
  edge((0, 0), (0, 0), `read()`, "--|>", bend: 130deg),
  pause,
  edge(`read()`, "-|>"),
  node((1, 0), `eof`, radius: 2em),
  pause,
  edge(`close()`, "-|>"),
  node((2, 0), `closed`, radius: 2em, extrude: (-2.5, 0)),
  edge((0, 0), (2, 0), `close()`, "-|>", bend: -40deg),
)


= 定理

== 素数

#definition[
  1より大きく、より小さい2つの自然数の積として表せない自然数を
  #highlight[_素数_] と呼びます。
]
#example[
  $2$、$3$、$17$ は素数です。
  @cor_largest_prime より、この一覧がすべてではないとわかります。
]

#theorem(title: "Euclid")[
  素数は無限に存在します。
]
#pagebreak(weak: true)
#proof[
  背理法で、すべての素数が $p_1, p_2, dots, p_n$ の有限個だと仮定します。
  $P = p_1 p_2 dots p_n$ とおきます。$P + 1$ は一覧にないため素数ではありません。
  したがって、ある素因数 $p_j$ が $P + 1$ を割ります。しかし $p_j$ は $P$ も割るので、
  差 $(P + 1) - P = 1$ も割ることになり、矛盾します。
]

#corollary[
  最大の素数は存在しません。
] <cor_largest_prime>
#corollary[
  合成数は無限に存在します。
]

#theorem[
  任意の長さだけ連続する合成数が存在します。
]

#proof[
  任意の $n > 2$ に対して、次を考えます。$
    n! + 2, quad n! + 3, quad ..., quad n! + n
  $
]


= その他

== 複数列

#cols[
  1列目の内容です。
][
  2列目の内容です。
]

== 高さを揃えた複数列のブロック

#cols(columns: (1fr, 1fr), gutter: 1em, lazy-layout: true)[
  #emph-block[
    高さを揃えた1列目： #lorem(10)
    #lazy-v(1fr)
  ]
][
  #emph-block[
    高さを揃えた2列目： #lorem(15)
    #lazy-v(1fr)
  ]
]


== 複数ページ

#lorem(200)


#show: appendix

= 付録

== 付録

現在のスライド番号に注目してください。
```

Touyingには多数の組み込みテーマがあり、たとえば `#show: university-theme.with()` でuniversityテーマを使えます。テーマの詳細については、後続の節を参照してください。