---
sidebar_position: 2
---

# セクションとサブセクション

## 構造

TouyingにもBeamerと同様にセクションとサブセクションの概念があります。

dewdropテーマなどでは第1・第2・第3レベルの見出しが、それぞれセクション、サブセクション、サブサブセクションに対応します。

```example
#import "@preview/touying:0.8.0": *
#import themes.dewdrop: *

#show: dewdrop-theme.with(aspect-ratio: "16-9")

= Section

== Subsection

=== Title

Hello, Touying!
```

サブセクションが不要なら、universityテーマのように第1レベルをセクション、第2レベルをスライドタイトルとして使用できます。

```example
#import "@preview/touying:0.8.0": *
#import themes.university: *

#show: university-theme.with(aspect-ratio: "16-9")

= Section

== Title

Hello, Touying!
```

この動作は `config-common` の `slide-level` で調整できます。`slide-level` は0から始まる見出しの階層を表します。`slide-level: 2` ではセクションとサブセクション、`slide-level: 3` ではサブサブセクションまでが新しいスライドの対象になります。

## 番号付け

セクションやサブセクションに番号を付けるには、次のようにします。

```typst
#set heading(numbering: "1.1")
#show heading.where(level: 1): set heading(numbering: "1.")
```

これにより `1.1` 形式の番号が付き、セクションは `1.` と表示されます。

## 目次

Touyingで目次を表示するのは簡単です。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *
#import "@preview/numbly:0.1.0": numbly

#set heading(numbering: numbly("{1}.", default: "1.1"))

#show: simple-theme.with(aspect-ratio: "16-9")

= Section

== Subsection

#components.adaptive-columns(outline(indent: 1em))
```

`outline(indent: 1em)` はTypst標準の目次関数です。`#components.adaptive-columns()` は列数を自動調整して目次を1ページに収めます。

進捗に対応する目次には `#components.progressive-outline()` や `#components.custom-progressive-outline()` が使えます。`outline.entry` を操作して独自に作成することもでき、その際は `#utils.section-relationship` が役立ちます。

## 特殊な見出しラベル

Touyingは見出しの特殊なラベルを認識してスライドを制御します。見出しの表示を変更するものと、出力モードに応じて内容を絞るものがあります。

### 見出し自体の表示

これらのラベルは見出しにだけ影響し、スライド自体は**非表示になりません**。見出しの下の内容は通常どおり描画され、スライドカウンターも進みます。

| ラベル | 効果 |
|-------|--------|
| `<touying:skip>` | セクションスライドを作成しません。見出しは表示され番号も付きます。 |
| `<touying:hidden>` | セクションスライドを生成せず、見出しを番号・目次・PDFブックマークから除外します。 |
| `<touying:unnumbered>` | 見出しの番号付けを無効にします。スライドカウンターには影響しません。 |
| `<touying:unoutlined>` | 見出しを `outline()` から除外します。 |
| `<touying:unbookmarked>` | 見出しのPDFブックマークを生成しません。 |

:::warning[注意]

`<touying:hidden>` はスライド自体を隠しません。見出しの下の内容は通常のスライドとして表示され、番号も進みます。`<touying:unnumbered>` も見出し番号だけに影響します。スライドのカウントを止めるには `config-common(freeze-slide-counter: true)` を指定します。

:::

### 出力モードによる絞り込み

次のラベルは出力モードで内容を絞ります。現在のモードに一致しなければ、対象のスライドまたは `#slide[..]` ブロック全体を除外します。

| ラベル | 効果 |
|-------|--------|
| `<touying:presentation>` | プレゼンテーションモードでのみ表示します。 |
| `<touying:handout>` | 配布資料モードでのみ表示します。 |
| `<touying:slides>` | スライド出力でのみ表示します。`<touying:presentation-handout>` の短縮形です。 |
| `<touying:article>` | 記事モードでのみ表示します。 |
| `<touying:never>` | どのモードでも表示しません。 |

キーワードをハイフンでつなぐと論理和になります。`<touying:handout-presentation>` は `<touying:slides>` と等価で、`<touying:presentation-article>` はプレゼンテーションと記事モードで表示されます。

例外として `<touying:never>` は空のモード一覧を表します。`<touying:never-presentation>` は特殊なキーワードではなく通常のラベルです。

下書きや別案など、削除せずにすべての出力から除外したいスライドに便利です。`#pause` やラベルを含むマークアップをコメントアウトする必要はありません。

ラベルは見出しにもスライド全体にも指定できます。

```typst
== Only in the handout <touying:handout>

#slide[Only in the presentation] <touying:presentation>
```

たとえば `<touying:hidden>` は目次スライドの見出しを番号付け・目次・PDFブックマークから除外します（スライド自体は表示されます）。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *
#import "@preview/numbly:0.1.0": numbly

#set heading(numbering: numbly("{1}.", default: "1.1"))

#show: simple-theme.with(aspect-ratio: "16-9")

== Outline <touying:hidden>

#components.adaptive-columns(outline(title: none, indent: 1em))

= First Section

== Slide One

Content.
```

## 付録

`appendix` 関数は総スライド数を表す**分母**の `utils.last-slide-number` を固定します。`utils.slide-counter` は進み続けるため、付録のフッターが `4 / 2` のようになる場合があります。

スライド番号自体も進めない場合は `config-common(freeze-slide-counter: true)` を使ってください。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme

= Main Section

== Introduction

Main content here. Check the slide number in the footer.

#show: appendix

= Appendix

== Appendix Slide

The denominator is frozen at the last main-section slide; the slide number keeps advancing.
```
