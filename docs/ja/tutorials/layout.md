---
sidebar_position: 4
---

# ページレイアウト

## 基本概念

Typstで見栄えのよいスライドを作るには、ページモデルを正しく理解する必要があります。ページのスタイルを変更しない場合は読み飛ばしても構いませんが、一読することを推奨します。

具体例を使ってTypstの既定のページモデルを説明します。

```example
#let container = rect.with(height: 100%, width: 100%, inset: 0pt)
#let innerbox = rect.with(stroke: (dash: "dashed"))

#set text(size: 30pt)
#set page(
  paper: "presentation-16-9",
  header: container[#innerbox[ヘッダー]],
  header-ascent: 30%,
  footer: container[#innerbox[フッター]],
  footer-descent: 30%,
)

#place(top + right)[Margin→]
#container[
  #container[
    #innerbox[内容]
  ]
]
```

次の概念を区別する必要があります。

1. **モデル：** TypstにはCSSのボックスモデルに似た概念があり、マージン、パディング、内容に分かれます。ただしパディングは `set page(..)` のプロパティではなく、`#pad(..)` を明示して追加します。
2. **マージン：** ページの上下左右に設ける余白です。Typstのページモデルの中心となり、ほかのプロパティ、特にヘッダーとフッターに影響します。ヘッダーとフッターは実際にはマージン領域内に配置されます。
4. **ヘッダー：** ページ上部に配置される内容で、コンテナーと内部ボックスに分かれます。コンテナーとパディングの端にある隙間は `header-ascent: 30%` に相当し、割合の基準は上マージンです。内部ボックスはコンテナーの左下に配置され、既定の配置は `#set align(left + bottom)` です。
5. **フッター：** ページ下部の内容です。ヘッダーと似ていますが、向きが逆になります。
6. **配置：** `place` 関数は親コンテナーを基準に要素を絶対配置し、ほかの要素のレイアウトに影響を与えません。`alignment`、`dx`、`dy` を指定でき、ロゴなどの装飾要素の配置に適しています。

したがって、Typstでスライドを作るには次のように設定します。

```typst
#set page(
  margin: (x: 4em, y: 2em),
  header: align(top)[ヘッダー],
  footer: align(bottom)[フッター],
  header-ascent: 0em,
  footer-descent: 0em,
)
```

ただし、ヘッダーをページ幅全体に広げるには追加の工夫が必要です。ここでは負のパディングを使用します。

```example
#let container = rect.with(stroke: (dash: "dashed"), height: 100%, width: 100%, inset: 0pt)
#let innerbox = rect.with(fill: rgb("#d0d0d0"))
#let margin = (x: 4em, y: 2em)

// ヘッダーとフッターに負のパディングを指定
#let negative-padding = pad.with(x: -margin.x, y: 0em)

#set text(size: 30pt)
#set page(
  paper: "presentation-16-9",
  margin: margin,
  header: negative-padding[#container[#align(top)[#innerbox(width: 100%)[ヘッダー]]]],
  header-ascent: 0em,
  footer: negative-padding[#container[#align(bottom)[#innerbox(width: 100%)[フッター]]]],
  footer-descent: 0em,
)

#place(top + right)[↑Margin→]
#container[
  #container[
    #innerbox[内容]
  ]
]
```

## ページ管理

Typstでは `set page(..)` でページ設定を変更すると、現在のページの変更ではなく新しいページが生成されます。そのためTouyingは `self.page` メンバー変数を保持します。

先ほどの例は次のように書き換えられます。

```typst
#show: default-theme.with(
  config-page(
    margin: (x: 4em, y: 2em),
    header: align(top)[ヘッダー],
    footer: align(bottom)[フッター],
    header-ascent: 0em,
    footer-descent: 0em,
  ),
)
```

`config-common(zero-margin-header: true)`（`self.zero-margin-header = true` と等価）が設定されている場合、Touyingは `margin.x` を自動的に調べ、ヘッダーに負のパディングを適用するか判断します。

同様に、テーマのヘッダーやフッターが好みに合わない場合は、次のように変更できます。

```typst
config-page(footer: [独自のフッター])
```

:::warning[注意]

`set page(..)` はTouyingによって上書きされる可能性があるため、直接使用しないでください。

:::

この方法なら `self.page` を通じて現在のページ設定をリアルタイムで取得でき、マージンや背景色を参照する関数で役立ちます。コンテキストから値を取得する方法に似ていますが、実際にはより便利に使用できます。

## ページの段組み

ページを2列や3列に分ける場合は、Touyingの標準 `slide` 関数にある `composer` 機能を使えます。最も簡単な例は次のとおりです。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide[
  1列目の内容です。
][
  2列目の内容です。
]
```

列の分割方法は `slide` の `composer` パラメーターで変更できます。既定値は `cols.with(columns: auto, gutter: 1em)` です。左列に残りの幅を割り当てたい場合は次のようにします。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide(composer: (1fr, auto))[
  1列目の内容です。
][
  2列目の内容です。
]
```

## `lazy-v` で列の高さを揃える

`cols` や `grid` を使った段組みでは、内容量によって列の高さが異なります。ラベルやキャプションなどを各列の下端に揃える場合や、すべての列を最も高い列に合わせる場合は、`lazy-v` と `lazy-layout` を組み合わせます。

### 動作の仕組み

- **`lazy-v(1fr)`** — ブロックの本文とフッターの間に置きます。高さの測定中には無視される、遅延評価型の垂直スペーサーです。
- **`lazy-layout`** — 段組み全体を囲みます。まず `lazy-v` を無視して各列の自然な高さを測り、最大の高さを固定してから `lazy-v` を有効にして再描画します。ページ全体に広げず、最も高い列に各列を揃えます。

### `cols` を使う方法（推奨）

`cols` では `lazy-layout` が既定で無効です。`lazy-layout: true` を渡し、各ブロック内に `lazy-v(1fr)` を追加します。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#cols(lazy-layout: true)[
  #block(fill: luma(220), inset: .5em, radius: .2em, width: 100%)[
    #lorem(10)
    #lazy-v(1fr)
    左下の内容です。
  ]
][
  #block(fill: luma(220), inset: .5em, radius: .2em, width: 100%)[
    #lorem(20)
    #lazy-v(1fr)
    右下の内容です。
  ]
]
```
両列の高さが高い方に揃い、「Bottom left.」と「Bottom right.」が下端に配置されます。レイアウト全体の高さは最も高い列と同じで、ページ全体には**広がりません**。

:::note[注意]

これは `#slide[][]` 内で `v(1fr)` を使う場合とは異なります。`slide` のcomposerはページ全体の高さを占めるため `v(1fr)` が直接機能します。`lazy-v` はページ全体に広げず高さを揃えたい、独立した `cols` や `lazy-layout` 向けの機能です。

:::

### 手動のグリッドを使用する

`grid` を `lazy-layout` で直接囲むこともできます。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#lazy-layout(grid(
  columns: (1fr, 1fr),
  gutter: 1em,
  block(fill: luma(220), inset: .5em, radius: .2em, width: 100%)[
    #lorem(10)
    #lazy-v(1fr)
    左下の内容です。
  ],
  block(fill: luma(220), inset: .5em, radius: .2em, width: 100%)[
    #lorem(20)
    #lazy-v(1fr)
    右下の内容です。
  ],
))
```

:::tip[ヒント]

`cols` の既定値は `lazy-layout: false` です。高さを揃える場合だけ有効にしてください。

:::

## 内容のはみ出しを防ぐ

既定ではスライドの内容がページの高さを超えると、Touyingは次のページへ自動的に送ります。多くの場合は便利ですが、エージェントがスライドの境界を厳密に把握する必要があるワークフローなどでは、この動作を無効にしたいことがあります。

はみ出しによる改ページを防ぐには `config-common(breakable: false)` を指定します。

```typst
// はみ出しを防ぎ、超過時に警告する（breakable: false の既定動作）
#show: simple-theme.with(
  config-common(breakable: false),
)

// はみ出しを防ぎ、超過部分を切り取る
#show: simple-theme.with(
  config-common(breakable: false, clip: true),
)

// はみ出しを防ぎ、検出を無効化する（性能優先）
#show: simple-theme.with(
  config-common(breakable: false, detect-overflow: false),
)
```

関連するパラメーター：

- **`clip`**（既定値 `false`）：`true` にするとスライドの高さを超える内容を表示上切り取ります。
- **`detect-overflow`**（既定値 `true`）：レイアウトを測定し、内容が利用可能な高さを超えるとページ・スライド・サブスライドを示す**警告**を出します。コンパイルは停止しないため、ビルドを中断せずに問題を発見できます。測定の負荷を避けるには `false` にします。

:::note[注意]

`clip` と `detect-overflow` は `breakable: false` の場合にのみ有効です。

:::

`touying-set-config` を使って、プレゼンテーションの途中でこれらの設定を切り替えることもできます。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme.with(config-common(breakable: false))
== このスライドではみ出し部分が切り取られます

// 特定のスライドで切り取りを有効にする
#show: touying-set-config.with(config-common(clip: true))

#lorem(500)
```