---
sidebar_position: 2
---

# ナビゲーション要素

高度なスライドテーマではナビゲーション要素を組み込むことがあります。たとえば [dewdropテーマ](https://touying-typ.github.io/docs/themes/dewdrop)を参照してください。

## 左右のナビゲーション

[`lr-navigation`](https://touying-typ.github.io/docs/reference/core/lr-navigation) はクリック可能な「前へ」「次へ」操作を作成します。スライド単位、サブスライド（実際のページ）単位、または両方で移動できます。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(
  footer: self=>[
    #align(center,
      lr-navigation(
        self: self,
        mode: "both",
        show-useless: false,
        nav: (
          filled: sym.triangle.filled,
          stroked: sym.chevron,
        ),
      )
    )
  ],
)

= ナビゲーションの例

== Slide A

このスライドにはpauseがあります。

#pause

これは次のサブスライドで表示されます。

== Slide B

ページ単位のリンクでスライド全体の間を移動できます。
```

## ミニスライドのナビゲーション

[`components.mini-slides`](https://touying-typ.github.io/docs/reference/components/mini-slides) は、セクションとサブセクションへのリンクをスライドごとの小さな点とともに表示します。

サイドバーやプログレスバーを使わずに進捗をコンパクトに示したい場合、ヘッダーに配置すると効果的です。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(
	config-page(
		margin: (top: 4em, bottom: 2em, x: 2em),
		header-ascent: 2em,
	),
    header: self => components.mini-slides(
        self: self,
        fill: self.colors.primary,
        display-section: true,
        display-subsection: true,
        linebreaks: true,
        short-heading: true,
    ),
)

= はじめに

== Motivation

移動に合わせてミニスライドの表示が更新されます。

== Scope

別のサブセクションです。

= 方法

== Setup

Current section is highlighted.
```

## サイドバーのナビゲーション

Touyingには単独の `sidebar-navigation` 関数はありません。Dewdropテーマと同様に、[`components.custom-progressive-outline`](https://touying-typ.github.io/docs/reference/components/custom-progressive-outline) を使ってサイドバーを構成します。

最も簡単なのはDewdropの組み込みサイドバーを有効にし、オプションを調整する方法です。

```example
#import "@preview/touying:0.8.0": *
#import themes.dewdrop: *

#show: dewdrop-theme.with(
	aspect-ratio: "16-9",
	navigation: "sidebar",
	sidebar: (
		width: 11em,
		filled: false,
		numbered: true,
		indent: .8em,
		short-heading: true,
	),
)
#outline-slide()

= パートI <touying:skip>

== Problem

Sidebar highlights where you are in the outline.

== Constraints

Indented subsection entries.

= パートII

== Solution

現在のセクションとサブセクションが自動的に強調されます。
```

この方式を採用する場合は、dewdropテーマの実装を参考にしてください。
