---
sidebar_position: 3
---

# セクション用ユーティリティ

Touyingは各スライドに不可視の見出しを挿入するため、Typstの `query()` 関数を使用していつでも現在のセクションを問い合わせることができます。

## 現在の見出しを表示する

`utils.display-current-heading(level: N)` は、指定されたレベルにおける直近の見出しのテキストを返します。ほとんどのテーマがヘッダーの内容を設定するために使用しています。

```typst
// ヘッダーに現在のセクション（レベル1）を表示する
utils.display-current-heading(level: 1)

// 現在のサブセクション（レベル2）を表示する
utils.display-current-heading(level: 2)
```

`utils.display-current-short-heading(level: N)` は、番号付けを取り除く、より短い形式のバリエーションです。

```typst
utils.display-current-short-heading(level: 2)
```

## セクション名を含む独自のヘッダー

これらのユーティリティはカスタムヘッダーでも使用できます。

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

Touyingには進捗表示付きアウトラインのユーティリティがいくつかあります。最も簡単なのは次の方法です。

### 進捗表示付きアウトライン（既定）

[`components.progressive-outline()`](https://touying-typ.github.io/docs/reference/components/progressive-outline) は、現在のセクションを強調し、残りを灰色で表示するアウトラインを描画します。これはテーマ付きのプレゼンテーションでよく使われるパターンです。

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

[`components.adaptive-columns(outline(...))`](https://touying-typ.github.io/docs/reference/components/adaptive-columns) は別のバリエーションで、標準の [`outline()`](https://typst.app/docs/reference/model/outline/) を適切な数の列で囲み、1ページに収まるようにします。

### 独自の進捗表示付きアウトライン

[`components.custom-progressive-outline()`](https://touying-typ.github.io/docs/reference/components/custom-progressive-outline) は、進捗表示付きアウトラインに対してさまざまなスタイルのルールを指定できます。これによりはるかに柔軟になりますが、すべてを自分で指定する必要があります。 

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

すべてを自分で指定しなければならず、見た目のよいデフォルト値が用意されていないことに注意してください。また、一部のパラメーターは自動的に繰り返されますが、そうでないものもあります。この方法が好みでない場合は、単に `set` ルールを使ってアウトライン項目を直接調整することもできます。そのために、現在のセクションのコンテキストを取得するヘルパーを用意しています。

### セクションの関係を調べるヘルパー

ユーティリティ [`utils.section-relationship()`](https://touying-typ.github.io/docs/reference/utils/section-relationship) は、現在いるセクションと指定されたアウトライン項目の関係を取得できます。戻り値は整数で、(-2, -1, 0, 1, 2) のいずれかです。

負の値は文書内で先に宣言された見出し、正の値は後に宣言された見出しです。現在の見出し**および**その子だけが関係値 `0` を持ちます。

値-1と1は、現在のセクションが属する最上位見出しと同じ最上位見出しの下にある、ほかの見出しのために予約されています。実際の `outline.entry.level` と組み合わせれば、好みのアウトラインを構築するのに十分なはずです。

例えば、次のように使用できます。
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
#{// すべての最上位見出しと現在の最上位見出しのすべての階層を通常どおり表示する。
  // 後続の兄弟見出しとほかの最上位見出しは半透明にし、
  // 現在の項目は太字にし、それ以外はすべて赤にする。

  show outline.entry: it => {
    let relationship = utils.section-relationship(it)
    let current = utils.current-heading()
    let alpha = if relationship == -2 or relationship > 0 { 40% } else { 100% }
    let weight = if relationship == 0 and current.level == it.level {
      "bold"
    } else { "regular" }
    if it.level > 1 and calc.abs(relationship) > 1 {
      text(fill: red, it) //通常はここに `none` を指定します。
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
