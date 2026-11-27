---
sidebar_position: 4
---

# ページレイアウト

## 基本概念

Typstを使ってスタイリッシュなスライドを作成するには、Typstのページモデルを正しく理解することが不可欠です。ページスタイルのカスタマイズに関心がなければ、このセクションを読み飛ばしても構いません。ただし、それでも目を通すことを推奨します。

具体例を通して、Typstのデフォルトのページモデルを説明しましょう。

```example
#let container = rect.with(height: 100%, width: 100%, inset: 0pt)
#let innerbox = rect.with(stroke: (dash: "dashed"))

#set text(size: 30pt)
#set page(
  paper: "presentation-16-9",
  header: container[#innerbox[Header]],
  header-ascent: 30%,
  footer: container[#innerbox[Footer]],
  footer-descent: 30%,
)

#place(top + right)[Margin→]
#container[
  #container[
    #innerbox[Content]
  ]
]
```

次の概念を区別する必要があります。

1. **モデル：** TypstにはCSSのボックスモデルに似たモデルがあり、マージン、パディング、コンテンツに分かれています。ただし、パディングは `set page(..)` のプロパティではなく、手動で `#pad(..)` を追加することで得られます。
2. **マージン：** マージンはページの端の領域であり、上、下、左、右に分かれます。これはTypstのページモデルの中核であり、ほかのすべてのプロパティ、特にヘッダーとフッターはマージンの影響を受けます。ヘッダーとフッターは実際にはマージンの内部に位置します。
4. **ヘッダー：** ヘッダーはページ上部の内容であり、コンテナーと内部ボックスに分かれています。ヘッダーのコンテナーの端とパディングの端が揃っておらず、間に空間があることが分かります。これは実際には `header-ascent: 30%` であり、割合は上マージンを基準としています。また、ヘッダーの内部ボックスは実際にはコンテナーの左下隅に位置しています。つまり内部ボックスのデフォルトは `#set align(left + bottom)` です。
5. **フッター：** フッターはページ下部の内容であり、ヘッダーに似ていますが向きが逆です。
6. **配置：** `place` 関数は親コンテナーの内部にあるほかの要素に影響を与えず、親コンテナーを基準に絶対配置できます。`alignment`、`dx`、`dy` を指定できるため、ロゴのような装飾要素の配置に適しています。

したがって、Typstをスライドの作成に利用するには、次のように設定するだけです。

```typst
#set page(
  margin: (x: 4em, y: 2em),
  header: align(top)[Header],
  footer: align(bottom)[Footer],
  header-ascent: 0em,
  footer-descent: 0em,
)
```

しかし、ヘッダーがページの幅全体を占めるようにする方法については、まだ対処する必要があります。ここでは負のパディングを使ってこれを実現します。例えば：

```example
#let container = rect.with(stroke: (dash: "dashed"), height: 100%, width: 100%, inset: 0pt)
#let innerbox = rect.with(fill: rgb("#d0d0d0"))
#let margin = (x: 4em, y: 2em)

// ヘッダーとフッターの負のパディング
#let negative-padding = pad.with(x: -margin.x, y: 0em)

#set text(size: 30pt)
#set page(
  paper: "presentation-16-9",
  margin: margin,
  header: negative-padding[#container[#align(top)[#innerbox(width: 100%)[Header]]]],
  header-ascent: 0em,
  footer: negative-padding[#container[#align(bottom)[#innerbox(width: 100%)[Footer]]]],
  footer-descent: 0em,
)

#place(top + right)[↑Margin→]
#container[
  #container[
    #innerbox[Content]
  ]
]
```

## ページ管理

Typstでは `set page(..)` コマンドを使ってページパラメーターを変更すると、現在のページを変更するのではなく、新しいページが作成されます。したがって、Touyingは `self.page` メンバー変数を保持する方法を選択しています。

例えば、前の例は次のように書き換えられます。

```typst
#show: default-theme.with(
  config-page(
    margin: (x: 4em, y: 2em),
    header: align(top)[Header],
    footer: align(bottom)[Footer],
    header-ascent: 0em,
    footer-descent: 0em,
  ),
)
```

Touyingは `margin.x` の値を自動的に検出し、`config-common(zero-margin-header: true)` が設定されている場合にヘッダーへ負のパディングを適用するかを判断します。これは `self.zero-margin-header = true` と等価です。

同様に、特定のテーマのヘッダーやフッターのスタイルに満足していない場合は、次の方法で変更することもできます。

```typst
config-page(footer: [Custom Footer])
```

:::warning[注意]

したがって、`set page(..)` コマンドを自分で使用するべきではありません。Touyingによって上書きされる可能性があるためです。

:::

このアプローチにより、`self.page` を使って現在のページパラメーターをリアルタイムで問い合わせることもできます。これはページマージンや現在のページの背景色を取得する必要のある関数にとって非常に便利です。これはコンテキストから値を取得するルールとある程度等価であり、実際にはより便利に使用できます。

## ページの段組み

ページを2列または3列に分ける必要がある場合は、`composer` 機能を使用できます。これはTouyingのデフォルトの `slide` 関数が提供する機能です。最も簡単な例は次のとおりです。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide[
  First column.
][
  Second column.
]
```

列の分割方法を変更する必要がある場合は、`composer` パラメーター（`slide` のパラメーター）を変更できます。デフォルトのパラメーターは `cols.with(columns: auto, gutter: 1em)` です。左の列が残りの幅を占めるようにしたい場合は、次のようにできます。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide(composer: (1fr, auto))[
  First column.
][
  Second column.
]
```

## `lazy-v` で列の高さを揃える

複数列のレイアウトを使用すると（`cols` または手動の `grid` を使用した場合）、内容量が異なる列は高さも異なります。「フッター」に相当する内容（例えばラベルやキャプション）を各列の下端に押し下げてすべての列で揃えたい場合や、単にすべての列の高さを最も高い列に合わせたい場合は、`lazy-v` と `lazy-layout` を組み合わせて使用できます。

### 動作の仕組み

- **`lazy-v(1fr)`** — ブロック内の主要な内容とフッターの内容の間に配置します。高さの測定中は不可視となる、遅延評価される垂直スペーサーとして機能します。
- **`lazy-layout`** — 複数列のレイアウトを囲みます。まずすべての列の自然な高さを測定し（`lazy-v` マーカーは無視します）、次にマーカーを有効にして、その固定された高さで再描画します。これによって各列が最も高い列に合うように伸びますが、コンテナー全体がページ全体を埋めるまで拡張されることはありません。

### `cols` を使う方法（推奨）

`cols` はデフォルトでは `lazy-layout` を有効にしません。`lazy-layout: true` を `cols` に渡し、各ブロックの内部に `lazy-v(1fr)` を追加します。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#cols(lazy-layout: true)[
  #block(fill: luma(220), inset: .5em, radius: .2em, width: 100%)[
    #lorem(10)
    #lazy-v(1fr)
    Bottom left.
  ]
][
  #block(fill: luma(220), inset: .5em, radius: .2em, width: 100%)[
    #lorem(20)
    #lazy-v(1fr)
    Bottom right.
  ]
]
```
両方の列が同じ高さ（高い方の列に一致する高さ）になり、「Bottom left.」と「Bottom right.」は下端で揃います。レイアウト全体の高さは最も高い列と同じであり、ページ全体を埋めるまで拡張されることは**ありません**。

:::note[注意]

これは `v(1fr)` を `#slide[][]` 内で使用する場合とは異なります。`slide` のcomposerはページの高さ全体を占めるため、そこで `v(1fr)` が直接機能します。`lazy-v` は、ページ全体に拡張せずに高さを揃えたいときに、独立した `cols` または `lazy-layout` の呼び出しで使用するように設計されています。

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
    Bottom left.
  ],
  block(fill: luma(220), inset: .5em, radius: .2em, width: 100%)[
    #lorem(20)
    #lazy-v(1fr)
    Bottom right.
  ],
))
```

:::tip[ヒント]

`cols` はデフォルトでは `lazy-layout: false` です。高さを揃える必要がある場合にのみ有効にしてください。

:::

## 内容のはみ出しを防ぐ

デフォルトでは、スライドの内容がページの高さを超えると、Touyingは余分な内容を自動的に次のページへ送ります。ほとんどの場合、これは妥当ですが、ページとの対応関係を厳密に制御する必要がある状況、例えばエージェントがスライドの境界について推論する必要があるエージェント型ワークフローなどでは、この動作を無効にしたい場合があります。

内容が次のページにはみ出すのを防ぐには `config-common(breakable: false)` を使用します。

```typst
// はみ出しを防ぎ、はみ出した場合は警告する（breakable: false のデフォルト動作）
#show: simple-theme.with(
  config-common(breakable: false),
)

// はみ出しを防ぎ、はみ出した内容を視覚的に切り取る
#show: simple-theme.with(
  config-common(breakable: false, clip: true),
)

// はみ出しを防ぎ、はみ出しの検出を無効にする（性能優先）
#show: simple-theme.with(
  config-common(breakable: false, detect-overflow: false),
)
```

関連するパラメーター：

- **`clip`**（デフォルト `false`）：`true` の場合、スライドの高さを超えた内容が視覚的に切り取られます。
- **`detect-overflow`**（デフォルト `true`）：`true` の場合、レイアウトの測定が行われ、内容の高さが利用可能なスライドの高さを超えると、ページ、スライド、サブスライドを示す**警告**が出力されます。コンパイルは中止されず、文書は引き続きビルドされるため、ビルドを壊さず早期にはみ出しを検出できます。追加のレイアウト処理のオーバーヘッドを避けるには `false` に設定します。

:::note[注意]

`clip` と `detect-overflow` は `breakable: false` の場合にのみ有効になります。

:::

`touying-set-config` を使って、プレゼンテーションの途中でこれらの設定を動的に切り替えることもできます。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme.with(config-common(breakable: false))
== This slide's overflow will be clipped

// 特定のスライドでクリッピングを有効にする
#show: touying-set-config.with(config-common(clip: true))

#lorem(500)
```