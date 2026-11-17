---
sidebar_position: 4
---

# よくある質問

## テーマと設定

### テーマを選択・切り替えるには？

テーマをインポートし、`#show` で適用します。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *
#show: simple-theme
= Section

== Slide

This uses the simple theme.
```

利用可能なテーマ：`simple`、`default`、`metropolis`、`aqua`、`dewdrop`、`stargazer`、`university`。

### テーマの色を変更するには？

テーマへ `config-colors(...)` 引数を渡します。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(
  aspect-ratio: "16-9",
  config-colors(primary: rgb("#d94f00")),
  config-info(title: [Custom Color], author: [Author]),
)

= Section

== Slide

The header now uses the custom primary color.
```


### スライド本文から現在のテーマ色を取得するには？

テーマの色は `self.colors` に格納されています。通常のスライド本文では `touying-fn-wrapper-raw` で関数を囲み、Touyingから現在のスライドのコンテキストを `self` として受け取ります。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(aspect-ratio: "16-9")

= Section

== Slide

#touying-fn-wrapper-raw((self: none) => text(fill: self.colors.primary)[
  This text uses the current theme's primary color.
])

#touying-fn-wrapper-raw((self: none) => rect(
  fill: self.colors.secondary,
  width: 4em,
  height: 1em,
))
```

テーマメソッドやコールバックがすでに `self` を受け取る場合は、`self.colors.primary`、`self.colors.neutral-lightest` などで直接アクセスできます。

:::note
`touying-fn-wrapper` も使用できますが、`raw` 版はpauseの流れから抜けず、アニメーションメソッド内や自身の内部に入れ子にできます。[使用例](https://github.com/touying-typ/touying/blob/main/tests/features/alert/test.typ)も参照してください。
:::

---

## レイアウトと段組み

### 2段組みを作るには？

`slide` の `composer` 引数で内容を列に分割します。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *
#show: simple-theme
#slide(composer: (1fr, 1fr))[
  == Left Column

  Some text on the left side.
][
  == Right Column

  Some text on the right side.
]
```

等幅の場合は `composer` 引数を省略することもできます。
幅を変えるには `(2fr, 1fr)` のように比率を指定します。

### 内容を絶対位置に配置するには？

Typstの `place` 関数を使用します。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *
#show: simple-theme
#slide[
  Main slide content here.

  #place(bottom + right, dx: -1em, dy: -1em)[
    #rect(fill: blue.lighten(80%), inset: 0.5em)[Note]
  ]
]
```

### 内容をスライドの残りの高さ・幅に収めるには？

`utils.fit-to-height` または `utils.fit-to-width` を使用します。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *
#show: simple-theme
#slide[
  #utils.fit-to-width(1fr)[
    == This heading fills the slide width
  ]

  Some content below.
]
```

---

## 目次

### 目次を表示するには？

Typst標準の `outline` を `components.adaptive-columns` で囲みます。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(aspect-ratio: "16-9")

== Outline <touying:hidden>

#components.adaptive-columns(outline(title: none, indent: 1em))

= First Section

== Introduction

Hello, Touying!

= Second Section

== Details

More content here.
```

`<touying:hidden>` ラベルは、目次スライド自身の見出しを目次から除外します。


### メインの目次だけを残し、自動生成されるセクションスライドを無効にするには？

一部のテーマでは最上位のセクション（`=`）で目次などのセクションスライドを自動生成します。`config-common(new-section-slide-fn: none)` で無効化すれば、自分で記述したメインの目次スライドだけを残せます。

```example
#import "@preview/touying:0.8.0": *
#import themes.metropolis: *

#show: metropolis-theme.with(
  aspect-ratio: "16-9",
  config-common(new-section-slide-fn: none),
)

== Outline <touying:hidden>

#components.adaptive-columns(outline(title: none, indent: 1em))

= First Section

== First Slide

= Second Section

== Second Slide
```

### 目次のセクションに番号を付けるには？

`numbly` パッケージと `#set heading(numbering: ...)` を組み合わせます。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *
#import "@preview/numbly:0.1.0": numbly

#set heading(numbering: numbly("{1}.", default: "1.1"))

#show: simple-theme.with(aspect-ratio: "16-9")

== Outline <touying:hidden>

#components.adaptive-columns(outline(title: none, indent: 1em))

= First Section

== First Slide

= Second Section

== Second Slide
```

### 現在位置を強調する目次を表示するには？

`components.progressive-outline` で現在のセクションを強調します。

```example
#import "@preview/touying:0.8.0": *
#import themes.dewdrop: *

#show: dewdrop-theme.with(aspect-ratio: "16-9")

= First Section

== Outline

#components.progressive-outline()

= Second Section

== Slide
```

---

## 参考文献と引用

### 引用を脚注として表示するには？

`config-common(show-bibliography-as-footnote: true)` を設定し、スライドショーの末尾で参考文献を呼び出します。`"chicago-notes"` のように文献スタイルが自動で脚注化する場合は、この値を `true` にしないでください。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#let bib = bytes(
  "@book{knuth,
    title={The Art of Computer Programming},
    author={Donald E. Knuth},
    year={1968},
    publisher={Addison-Wesley},
  }",
)

#show: simple-theme.with(
  aspect-ratio: "16-9",
  config-common(show-bibliography-as-footnote: true),
)

= Citations

== Footnote Example

This is a famous book. @knuth

== Bibliography
#bibliography(bib)
```

脚注の引用を解決するには、参考文献を文書に含める必要があります。文献一覧を表示したくない場合は `hide(bibliography(bib))` としてください。

別の内容があるスライドで呼び出す場合は、複数回実行しないよう注意してください。
参考文献を置いたスライドに複数のサブスライドがあると、自動的に複数回実行される場合があります。その場合は `only("h", bibliography(bib))` で囲み、呼び出した段階で一度だけ出力してください。

---

## 発表者ノート

### スライドに発表者ノートを追加するには？

スライド内の任意の位置に `#speaker-note[...]` を記述します。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *
#show: simple-theme
#slide[
  == My Slide

  Visible content here.

  #speaker-note[
    - Remind the audience of the previous topic.
    - Emphasize the key takeaway.
    - Time check: should be at 10 min mark.
  ]
]
```

既定では発表者ノートはスライドに表示されません。

### 発表者ノートを別の画面に表示するには？

`config-common(show-notes-on-second-screen: right)` でノートをスライドの横に表示します。

```typst
#show: simple-theme.with(
  aspect-ratio: "16-9",
  config-common(show-notes-on-second-screen: right),
)
```

`top`、`bottom`、`left`、`right` の配置に対応します。指定した軸に沿ってページを2倍に広げ、追加された半分を余白側に配置するため、スライド自体の大きさは維持されます。

[pdfpc](https://pdfpc.github.io/) や [pympress](https://github.com/Cimbali/pympress) などの発表ツールに対応します。

---

## スライド番号と付録

### フッターにスライド番号を表示するには？

多くのテーマは既定でスライド番号を表示します。内容を変えるには、テーマのフッター用オプション（simpleテーマでは `footer-right`）を設定してください。現在の番号は `utils.slide-counter.display()`、総数は `utils.last-slide-number` で取得します。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *

#show: simple-theme.with(
  aspect-ratio: "16-9",
  footer-right: context [
    Slide #utils.slide-counter.display() of #utils.last-slide-number
  ],
)

= Section

== First Slide

The footer shows the slide number.

== Second Slide

Still counting.
```

### 見出しの番号をなくすには？

`<touying:unnumbered>` ラベルは見出しに `numbering: none` を指定します。スライド上でも目次でも番号が付かず、後続の見出し番号は欠番なしで続きます。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #set heading(numbering: "1.1")
>>> #show: simple-theme
= Section

== Numbered

This heading has a number.

== Unnumbered <touying:unnumbered>

This heading has no number.
```

### スライド総数に影響しない付録を作るには？

本文の後に `#show: appendix` を適用します。これ以降のスライドは総数に含まれません。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>>
>>> #show: simple-theme.with(aspect-ratio: "16-9")

= Main Content

== Introduction

This is slide 1.

== Results

This is slide 2.

#show: appendix

= Appendix

== Extra Material

This slide is in the appendix and does not increment the main counter.
```

---

## アニメーションと動的な内容

### `#pause` で内容を段階的に表示するには？

`#slide` 内の内容の間に `#pause` を配置します。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide[
  First point.

  #pause

  Second point revealed on click.

  #pause

  Third point revealed on second click.
]
```

### 特定のサブスライドだけに内容を表示するには？

`#only("...")` で特定のサブスライドだけに表示できます。領域を維持したまま表示・非表示を切り替えるには `#uncover("...")` を使います。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide[
  #only("1")[Shown on subslide 1 only.]
  #only("2-")[Shown from subslide 2 onward.]
  #uncover("3-")[Revealed on subslide 3, space reserved before.]
]
```

`"h"` はpauseで進行する現在のサブスライドを表します。`"!"` を付けると選択範囲を反転できます。
詳しくは[動的スライドの章](./tutorials/dynamic/simple.md)を参照してください。

### ほかのアニメーション命令や `context` 式の中で `#pause` が動作しない理由は？

`#pause` はメタデータの挿入を利用しており、`context { ... }` ブロック内では動作しません。代わりにコールバック形式の `slide` で `self.subslide` を参照できます。ただし `repeat` の回数は自分で指定する必要があります。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide(repeat: 3, self => {
  let (uncover, only) = utils.methods(self)
  [First content.]
  linebreak()
  uncover("2-")[Revealed on subslide 2.]
  linebreak()
  only("3")[Only on subslide 3.]
})
```

自動で段階数を数えるアニメーション関数は `touying-fn-wrapper` に基づいており、自身や `context` の内部へ入れ子にできません。一方、`touying-fn-wrapper-raw` は自分自身や `touying-fn-wrapper` を含むアニメーション関数の内部へ入れ子にできます。ただし `context` 内には配置できません。

### CeTZの図の中で `#pause` を使うには？

CeTZのキャンバスを `touying-reduce` または別名の `touying-diagram` で囲みます。アニメーション命令を `(•,)` のような配列構文で囲む点に注意してください。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#import "@preview/cetz:0.5.2"

#let cetz-canvas = touying-reduce.with(cetz)

#slide[
  #cetz-canvas({
    import cetz.draw: *
    rect((0, 0), (4, 3))
    (pause,)
    circle((2, 1.5), radius: 1)
  })
]
```

### Fletcherの図の中で `#pause` を使うには？

Fletcherの図を `touying-reduce` で囲むと、TouyingがFletcherのreducerバインディングを自動的に検出します。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#import "@preview/fletcher:0.5.8" as fletcher: diagram, node, edge

#let fletcher-diagram = touying-reduce.with(fletcher)

#slide[
  #fletcher-diagram(
    node((0, 0), [A]),
    edge("->"),
    pause,
    node((1, 0), [B]),
  )
]
```
Fletcherでは配列構文は不要ですが、使用しても構いません。

### サブスライドごとに異なる内容を表示するには？

`#alternatives` で複数の内容を切り替えます。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide[
  The answer is: #alternatives[42][*forty-two*][_the ultimate answer_].
]
```

### アニメーションのない配布資料モードを有効にするには？

テーマの設定に `config-common(handout: true)` を指定します。

```typst
#show: simple-theme.with(
  aspect-ratio: "16-9",
  config-common(handout: true),
)
```

配布資料モードでは各スライドの最後のサブスライドだけが出力されます。

---

## フォントとテキスト

### プレゼンテーションのフォントを変更するには？

テーマ設定の前後で `#set text(...)` ルールを使います。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.metropolis: *
>>>
>>> #show: metropolis-theme.with(
>>>  aspect-ratio: "16-9",
>>>  config-info(title: [Custom Font]),
>>> )

#set text(font: "New Computer Modern", size: 22pt)

= Section

== Slide

Text now uses the custom font.
```

数式については数式用フォントも設定します。

```typst
#show math.equation: set text(font: "New Computer Modern Math")
```

### 段落を両端揃えにするには？

`#set par(justify: true)` を使います。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme

#set par(justify: true)

== Justified Text

#lorem(40)

```

---

## 見出しとセクション

### 自動セクションスライドを無効にするには？

`config-common(new-section-slide-fn: none)` を指定します。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.metropolis: *
>>>
#show: metropolis-theme.with(
  aspect-ratio: "16-9",
  config-common(new-section-slide-fn: none),
  config-info(title: [No Auto Sections]),
)

= Section

== Slide

No automatic section slide was created for the `= Section` heading.
```

### セクションスライドに内容を記述するには？

Touyingでは深い階層のサブセクションも含め、既定で `config-common(receive-body-for-new-section-slide-fn: false)` になっています。このためセクションスライドの下に書いた内容は別のスライドへ配置されます。同じセクションスライドに表示したい場合は `true` にしてください。


```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.metropolis: *
>>>
#show: metropolis-theme.with(
  aspect-ratio: "16-9",
  config-info(title: [content slides next to section slides]),
  config-common(receive-body-for-new-section-slide-fn: true)
)

= Section
This goes on the section slide itself.
---
Here is my content for this section.

== Slide
And this works normally.
```
セクションスライドに発表者ノートを追加できるのは `receive-body-...` が `true` の場合だけです。

### セクションスライドを隠したり、見出しを目次・ブックマークから除外したりするには？

`<touying:hidden>` はその見出しの下のスライド内容をPDFから削除するわけではありません。生成される不可視の見出しを番号・目次・ブックマークから除外し、見出しにより自動生成されるセクション／サブセクションスライドを省略します。

そのため、PDFには含めるが目次やブックマークには載せず、追加のセクションスライドも作りたくない目次スライドなどに適しています。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *
#show: simple-theme
== Outline <touying:hidden>

#components.adaptive-columns(outline(title: none, indent: 1em))

= First Section

== First Slide

Hello, Touying!
```

スライドを一時的に除外したい場合は、その内容を削除またはコメントアウトしてください。

### スライドを表示したまま目次から除外するには？

`<touying:unoutlined>` ラベルを使います。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *
#show: simple-theme
== Outline <touying:hidden>

#components.adaptive-columns(outline(title: none, indent: 1em))

= Section

== Normal Slide

Appears in the outline.

== Interstitial Slide <touying:unoutlined>

This slide shows but is not listed in the outline.

== Another Normal Slide

Also appears in the outline.
```

### 新しいスライドを作成する見出しレベルを変更するには？

`config-common(slide-level: ...)` を使います。既定値はテーマによって異なります。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(
  aspect-ratio: "16-9",
  config-common(slide-level: 2),
)

= Section

This text is part of the section slide.

== Subsection Slide

Each `==` heading creates a new slide.

=== Sub-subheading

Sub-subheadings do not create new slides.
```

### 独自のヘッダーやフッターを追加するには？

`config-page(header: ..., footer: ...)` を使用します。

```example
#import "@preview/touying:0.8.0": *
#import themes.default: *

#show: default-theme.with(
  aspect-ratio: "16-9",
  config-page(
    header: text(gray)[My Custom Header],
    footer: context align(right, text(gray)[
      Slide #utils.slide-counter.display()
    ]),
  ),
)

= Section

== Slide

Slide with a custom header and footer.
```

---

## config-commonリファレンス

### スライドの内容が次のページへはみ出すのを防ぐには？

`config-common(breakable: false)` を指定すると、次のページへの自動的なはみ出しを防げます。既定の `breakable: true` では内容がページの高さを超えると改ページされます。`false` では改ページ不可のブロックに内容を収めるため、スライドと出力ページを厳密に一対一で対応付ける必要があるエージェントのワークフローなどで便利です。

関連パラメーター：

- **`clip`**（既定値 `false`）：`true` ならスライドの高さを超える部分を表示上切り取ります。
- **`detect-overflow`**（既定値 `true`）：レイアウトを測定し、内容が利用可能な高さを超えた場合に警告します。コンパイルは続行されます。追加の測定処理を避けるには `false` にします。

```typst
// Prevent overflow, warn on overflow (default behavior when breakable: false)
#show: simple-theme.with(
  config-common(breakable: false),
)

// Prevent overflow and visually clip overflowing content
#show: simple-theme.with(
  config-common(breakable: false, clip: true),
)

// Prevent overflow, disable overflow detection (performance-first)
#show: simple-theme.with(
  config-common(breakable: false, detect-overflow: false),
)
```

`touying-set-config` でプレゼンテーションの途中に設定を切り替えることもできます。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme.with(config-common(breakable: false))
== This slide's overflow will be clipped

// Enable clipping for a specific slide
#show: touying-set-config.with(config-common(clip: true))

#lorem(500)
```

### 完全に隠す代わりに半透明にするには？

`config-methods(cover: utils.alpha-changing-cover)` を使用します。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(
  aspect-ratio: "16-9",
  config-methods(cover: utils.alpha-changing-cover),
)

= Section

== Slide

#pause
This content is shown with a low alpha cover.
```

コンパイルがより高速になると考えられる `utils.color-changing-cover` も使用できます。
既定値は `utils.hiding-cover` です。

### 脚注マーカーの見た目を変更するには？

`config-common(footnote-style: ..)` に、通常なら `show footnote: ..` に渡す関数を指定します。

```typst
#show: simple-theme.with(
  config-common(footnote-style: it => super(emph(it))),
)
```

独自の `show footnote: it => ..` ルールは実際に表示されている脚注にだけ適用されます。`#pause` によって隠された脚注はまだ本物の脚注ではなく、Touyingが幅を確保する仮のマーカーを描画します。そのため独自ルールでは見た目が一致しません。`footnote-style` は両方に適用されます。

隠された脚注の描画方法と独自の `cover` メソッドの要件は、[独自のカバー関数を作成する](./tutorials/dynamic/cover.md#writing-your-own-cover-function)を参照してください。

### 前処理でスライドの前に内容を挿入するには？

`config-common(preamble: ...)` は最初のスライドの前に一度実行されます。各サブスライドの前に何を配置するかはテーマが決めます。たとえば `simple` はスライドの見出しを表示し、独自の `subslide-preamble` 引数を受け取ります。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(
  aspect-ratio: "16-9",
  config-common(preamble: text(gray)[This appears once, before the first slide's content]),
  subslide-preamble: [Special prelude for each subslide],
)

= Section

== Slide

Content here.

#pause

More content.
```

実行タイミングに応じて `preamble`、`slide-preamble`、`subslide-preamble`、`page-preamble` があります。`page-preamble` だけがスライドのヘッダーより前に実行され、ほかはそれぞれの対象の最初のページのヘッダーの後に実行されます。

### `---` でスライドを区切るには？

`horizontal-line-to-pagebreak: true`（既定値）を設定し、`---` で改ページします。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(
  aspect-ratio: "16-9",
  config-common(horizontal-line-to-pagebreak: true),
)

= First Section

== Slide One

Content here.

---

== Slide Two

Separated by horizontal rule.
```

---

## テストと開発

### Touyingのテストを実行するには？

Touyingは [tytanic](https://github.com/Myriad-Dreamin/tytanic) テストフレームワークを使用します。次のようにインストールします。

```bash
cargo binstall tytanic
```

テストは次のコマンドで実行します。

```bash
tt run
```

テストは `tests/` ディレクトリ内に整理されています。

- `features/` — コア機能のテスト
- `themes/` — テーマ固有のテスト
- `integration/` — サードパーティーパッケージとの連携テスト（cetz、fletcher、pinit、theorion、codly、mitex）
- `issues/` — 報告された問題の回帰テスト
- `examples/` — ドキュメント内のサンプルのテスト

### Touyingに貢献するには？

Touyingに貢献する手順は次のとおりです。

1. GitHubでリポジトリをフォークします。
2. 変更用の新しいブランチを作成します。
3. 既存のコードスタイルに従って変更します。
4. [typstyle](https://github.com/Myriad-Dreamin/typstyle) でコードを整形します。
5. `tt run` を実行し、すべてのテストが成功することを確認します。
6. 変更内容を明確に説明したPull Requestを提出します。

---

## その他

### タイトル・著者・日付などを設定するには？

`config-info(...)` を使用します。

```example
#import "@preview/touying:0.8.0": *
#import themes.metropolis: *

#show: metropolis-theme.with(
  aspect-ratio: "16-9",
  config-info(
    title: [My Presentation],
    subtitle: [A Subtitle],
    author: [Jane Doe],
    date: datetime.today(),
    institution: [My University],
    contact: [contact\@mail.com],
  ),
)

#title-slide()

= Introduction

== First Slide

Content here.
```

### 1つのスライドだけ設定を上書きするには？

変更する内容の周囲で `touying-set-config` を使います。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *
#show: simple-theme
#slide[
  Normal slide.
]

#touying-set-config(config-page(fill: rgb("#fff3cd")))[
  #slide[
    This slide has a yellow background.
  ]
]

#slide[
  Back to normal.
]
```


### 全体またはスライド単位の設定を取得するには？

設定を取得したい位置で `touying-get-config` を使用します。設定は `context` 時にしか参照できないため、この値を使った計算には制約が生じる場合があります。
> ローカル設定を持つスライド内で使用すると、グローバル設定ではなくローカル設定が返されます。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *
#show: simple-theme.with(
  config-info(author: "Beautiful Name")
)
#slide(config:config-colors(primary: rgb("ABCDEF")))[
  #touying-get-config().info.author
  
  #touying-get-config().colors.primary
]
```

### Touyingのプレゼンテーションをコンパイルするには？

TouyingはTypstのパッケージなので、別のビルド手順は不要です。

```bash
typst compile slides.typ
```

編集中のライブプレビューには次の方法があります。

```bash
typst watch slides.typ
```

または [Tinymist](https://marketplace.visualstudio.com/items?itemName=myriad-dreamin.tinymist) のVS Code拡張機能を使うと、エディター内ですぐにプレビューできます。

### 複数ファイルでプレゼンテーションを作るには？

メインのエントリーファイルから `lib.typ` をインポートし、各セクションを `include` で読み込みます。

```typst
// main.typ
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(aspect-ratio: "16-9")

#include "intro.typ"
#include "methods.typ"
#include "results.typ"
```

読み込まれるファイルでは通常どおり見出しを使用できます。ファイルごとの追加のインポートは不要です。

### 現在のセクション名をヘッダーやフッターに表示するには？

`utils.display-current-heading(...)` または `utils.display-current-short-heading(...)` を使います。

```example
#import "@preview/touying:0.8.0": *
#import themes.default: *

#show: default-theme.with(
  aspect-ratio: "16-9",
  config-page(
    header: context text(gray)[
      #utils.display-current-heading(level: 1)
    ],
  ),
)

= My Section

== Slide

The header shows the current section name.
```

### `pinit` と組み合わせて注釈を付けるには？

両方のパッケージをインポートし、スライド内で通常どおり `#pin` や `#pinit-highlight` を使います。

```example
#import "@preview/touying:0.8.0": *
#import "@preview/pinit:0.2.2": *
#import themes.simple: *

#show: simple-theme.with(aspect-ratio: "16-9")

#slide[
  A #pin(1)key term#pin(2) to highlight.

  #pinit-highlight(1, 2)
]
```

ピンの表示をアニメーション化するには、`#pause` とpinitが正しく連携するようコールバック形式のスライドを使用します。

### サブスライド間で図や数式のカウンターを固定するには？

Touyingは一般的なカウンターをサブスライド間で既定で固定します。追加のカウンターを固定するには `frozen-counters` にカウンターの配列を渡します。

```typst
#show: simple-theme.with(
  config-common(frozen-counters: (counter("my-custom-counter"),)),
)
```

### 警告を有効・無効にするには？

Touyingは `touying` 名前空間の `uniwarn` で警告を出します。
関数はTouyingに結び付けられているため、直接次のように使用できます。

```typst
#import "@preview/touying:0.8.0": *

//to disable the warnings emitted by touying
#touying-disable-warnings()
//to reenable the warnings emitted by touying
#touying-enable-warnings()
```

次の方法も使用できます。

```typst
#import "@preview/uniwarn:0.1.1"
#uniwarn.disable-warnings("touying")
#uniwarn.enable-warnings("touying")
```