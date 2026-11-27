---
sidebar_position: 2
---

# ナビゲーション要素

より高度なスライドテーマでは、ナビゲーション要素を組み込むことが一般的です。例えば、[dewdropテーマ](https://touying-typ.github.io/docs/themes/dewdrop)を参照してください。

## 左右のナビゲーション

[`lr-navigation`](https://touying-typ.github.io/docs/reference/core/lr-navigation) は、クリック可能な「前へ」「次へ」のコントロールを作成します。スライドやサブスライド（物理的なページ）の単位、またはその両方で移動できます。

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

= Navigation Demo

== Slide A

This slide has a pause.

#pause

This appears on the next subslide.

== Slide B

Now the page-level links can jump between full slides.
```

## ミニスライドのナビゲーション

[`components.mini-slides`](https://touying-typ.github.io/docs/reference/components/mini-slides) は、セクションとサブセクションへのリンクを、スライドごとの小さなドットとともに表示します。

サイドバー全体やプログレスバーを使わず、コンパクトに進捗を示したい場合には、このパターンをヘッダーで使うと効果的です。

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

= Introduction

== Motivation

The mini-slides row updates as you move.

== Scope

Another subsection.

= Methods

== Setup

Current section is highlighted.
```

## サイドバーのナビゲーション

Touyingは単独の `sidebar-navigation` 関数を提供していません。実際には、Dewdropテーマで行われているように、[`components.custom-progressive-outline`](https://touying-typ.github.io/docs/reference/components/custom-progressive-outline) を使ってサイドバーを構築します。

このスタイルを使う最も手早い方法は、Dewdropの組み込みサイドバーナビゲーションを有効にし、そのオプションを調整することです。

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

= Part I <touying:skip>

== Problem

Sidebar highlights where you are in the outline.

== Constraints

Indented subsection entries.

= Part II

== Solution

The active section and subsection are emphasized automatically.
```

このアプローチを再現したい場合は、dewdropテーマでどのように実装されているかを見てみてください。
