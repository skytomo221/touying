---
sidebar_position: 3
---

# セクション用ユーティリティ

Touyingは各スライドに不可視の見出しを挿入するため、Typstの `query()` 関数で現在のセクションをいつでも取得できます。

## 現在の見出しを表示する

`utils.display-current-heading(level: N)` は指定したレベルで直近の見出しの文字列を返します。多くのテーマがヘッダーの表示に使っています。

```typst
// Show the current section (level 1) in the header
utils.display-current-heading(level: 1)

// Show the current subsection (level 2)
utils.display-current-heading(level: 2)
```

`utils.display-current-short-heading(level: N)` は番号を除いた短い見出しを返します。

```typst
utils.display-current-short-heading(level: 2)
```

## セクション名を含む独自のヘッダー

これらのユーティリティは独自のヘッダーにも使用できます。

```example
#import "@preview/touying:0.8.0": *
#import themes.default: *

#show: default-theme.with(
  aspect-ratio: "16-9",
  config-page(
    header: [
      #text(gray, utils.display-current-heading(level: 2))
      #h(1fr)
      #context utils.slide-counter.display()
    ],
  ),
)

= My Section

== First Slide

Header shows "First Slide" on the right side.

== Second Slide

Header updates automatically.
```

## 段階的に変化するアウトライン

Touyingには進捗に応じて変化するアウトラインのユーティリティが複数あります。最も簡単なのは次の方法です。

### 進捗表示付きアウトライン（既定）

[`components.progressive-outline()`](https://touying-typ.github.io/docs/reference/components/progressive-outline) は現在のセクションを強調し、それ以外を灰色で示すアウトラインを描画します。テーマ付きプレゼンテーションでよく使われる形式です。

```example
#import "@preview/touying:0.8.0": *
#import themes.dewdrop: *

#show: dewdrop-theme.with(aspect-ratio: "16-9")

= Introduction

== Overview <touying:hidden>

#components.progressive-outline()

= Background

== Slide

Content.
```

[`components.adaptive-columns(outline(...))`](https://touying-typ.github.io/docs/reference/components/adaptive-columns) は通常の [`outline()`](https://typst.app/docs/reference/model/outline/) を適切な列数に分け、1ページに収める方法です。

### 独自の進捗表示付きアウトライン

[`components.custom-progressive-outline()`](https://touying-typ.github.io/docs/reference/components/custom-progressive-outline) は進捗表示付きアウトラインのさまざまな装飾ルールを指定できます。自由度が高い反面、自分で設定する項目も多くなります。 

```example
#import "@preview/touying:0.8.0": *
#import themes.dewdrop: *

#show: dewdrop-theme.with(aspect-ratio: "16-9")

= Introduction

== Overview <touying:hidden>

#components.custom-progressive-outline(
  level: 1,
  show-past: (true, false),
  show-future: (true, false),
  show-current: (true, true, false),
  vspace: (.5em, .0em),
  numbering: ("1.1",),
  numbered: (true,true),
  title: none,
)

= Background

== Slide

Content.
```

見た目の整った既定値がないため、各項目を自分で指定する必要があります。パラメーターによっては値が自動的に繰り返されるものと、そうでないものがあります。`set` ルールでアウトライン項目を直接変更する方法もあります。そのために現在のセクション情報を取得するヘルパーも用意されています。

### セクションの関係を調べるヘルパー

[`utils.section-relationship()`](https://touying-typ.github.io/docs/reference/utils/section-relationship) は現在のセクションと指定したアウトライン項目の関係を調べます。返り値は `-2`、`-1`、`0`、`1`、`2` のいずれかです。

負の値は現在より前に、正の値は後に現れる見出しを表します。現在の見出し**および**その子要素の関係値だけが `0` です。

`-1` と `1` は、現在のセクションと同じ最上位見出しに属する別の見出しを表します。実際の `outline.entry.level` と組み合わせれば、柔軟なアウトラインを構成できます。

たとえば次のように使えます。
```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
>>> #set heading(numbering: "1.1")

= Start
== Start Sub
#lorem(5)
= My content
== My heading
#lorem(5)
---
#{// displays all top levels and all levels of the current top-level normally,
  // with future siblings and other top levels semi-transparent
  // the current entry bold and all others red.

  show outline.entry: it => {
    let relationship = utils.section-relationship(it)
    let current = utils.current-heading()
    let alpha = if relationship == -2 or relationship > 0 { 40% } else { 100% }
    let weight = if relationship == 0 and current.level == it.level {
      "bold"
    } else { "regular" }
    if it.level > 1 and calc.abs(relationship) > 1 {
      text(fill: red, it) //normally you put `none` here.
    } else {
      text(fill: utils.update-alpha(text.fill, alpha), weight: weight, it)
    }
  }
  outline(title: none)
}
---
=== Subsubheading
#lorem(3)

== Another heading
#lorem(5)

= Next Top Level

== Subsection
#lorem(5)
```
