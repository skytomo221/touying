---
sidebar_position: 2
---

# セクションとサブセクション

## 構造

Beamerと同様に、Touyingにもセクションとサブセクションという概念があります。

一般的には、dewdropテーマのように、第1レベル、第2レベル、第3レベルの見出しは、それぞれセクション、サブセクション、サブサブセクションに対応します。

```example
#import "@preview/touying:0.8.0": *
#import themes.dewdrop: *

#show: dewdrop-theme.with(aspect-ratio: "16-9")

= Section

== Subsection

=== Title

Hello, Touying!
```

しかし、サブセクションが必要ない場合も多いため、universityテーマのように、第1レベルと第2レベルの見出しを、それぞれセクションとタイトルに対応させることもあります。

```example
#import "@preview/touying:0.8.0": *
#import themes.university: *

#show: university-theme.with(aspect-ratio: "16-9")

= Section

== Title

Hello, Touying!
```

実際には、`slide-level` という `config-common` 関数のパラメーターを使って、この動作を制御できます。`slide-level` はネスト構造の深さを表し、0から始まります。例えば、`#show: university-theme.with(config-common(slide-level: 2))` と指定すると、`section`（セクション）と `subsection`（サブセクション）の両方で新しいスライドが作成されます。一方、`#show: university-theme.with(config-common(slide-level: 3))` と指定すると、`section`（セクション）、`subsection`（サブセクション）、`subsubsection`（サブサブセクション）のすべてで新しいスライドが作成されます。

## 番号付け

セクションとサブセクションに番号を付けるには、単に次のようにします。

```typst
#set heading(numbering: "1.1")
#show heading.where(level: 1): set heading(numbering: "1.")
```

これにより、デフォルトの番号付けは `1.1` になり、セクションには `1.` の番号付けが対応します。

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

`outline(indent: 1em)` はTypst標準の目次用関数です。`#components.adaptive-columns()` 関数は、`#columns(1, body)` や `#columns(2, body)` などを設定して調整することで、目次が1ページだけに収まるようにします。

現在の進捗を表示できる `outline` 関数が必要な場合は、dewdropテーマで使われているような `#components.progressive-outline()` や `#components.custom-progressive-outline()` の使用を検討するとよいでしょう。または、`outline.entry` 要素を操作して独自の関数を記述することもできます。特定の効果を実現するには、`#utils.section-relationship` を使うとよい場合があります。

## 特殊な見出しラベル

Touyingは見出しに付けられた特殊なラベルを認識し、スライドの動作を制御します。これらは、見出し自体の表示方法を変更するラベルと、出力モードによって内容を絞り込むラベルの2つのグループに分かれます。

### 見出し自体の表示

これらのラベルは見出しにのみ影響します。スライドが**消えることはありません**。ラベルが付いた見出しの下の内容は、引き続き通常のスライドとして描画され、スライドカウンターも進みます。

| ラベル | 効果 |
|-------|--------|
| `<touying:skip>` | 見出しによって新しいセクションのスライドは作成されません。見出し自体は引き続き描画され、番号も付けられます。 |
| `<touying:hidden>` | `skip` と同様に、新しいセクションのスライドは作成されません。さらに見出しには `numbering: none`、`outlined: false`、`bookmarked: false` が設定されるため、番号が付かず、`outline()` から除外され、PDFブックマークも生成されません。 |
| `<touying:unnumbered>` | 見出しに `numbering: none` を設定するため、見出し番号は付けられません。スライドカウンターとは無関係です。 |
| `<touying:unoutlined>` | 見出しが `outline()` から除外されます。 |
| `<touying:unbookmarked>` | この見出しのPDFブックマークは生成されません。 |

:::warning[注意]

`<touying:hidden>` はスライドの出力を抑制するものではありません。このラベルが付いた見出しはセクションスライドを生成しませんが、その下の内容は依然として通常のスライドであり、スライド番号を1つ占めます。同様に、`<touying:unnumbered>` は見出しの番号付けにのみ影響し、スライドカウンターの増加を止めるものではありません。スライドをカウンターの対象から完全に除外するには、`config-common(freeze-slide-counter: true)` を使用します。

:::

### 出力モードによる絞り込み

2つ目のグループのラベルは、出力モードによって内容を絞り込みます。ラベルが現在のモードに一致しない場合、そのラベルが付けられた対象全体、すなわち見出しの下のスライド、またはラベル付きの `#slide[..]` ブロックがスキップされます。

| ラベル | 効果 |
|-------|--------|
| `<touying:presentation>` | プレゼンテーションモード（`handout: false`）でのみ描画されます。 |
| `<touying:handout>` | 配布資料モード（`handout: true`）でのみ描画されます。 |
| `<touying:slides>` | スライド出力でのみ描画されます。`<touying:presentation-handout>` の短縮形です。 |
| `<touying:article>` | 記事モードでのみ描画されます。 |
| `<touying:never>` | どの出力モードでも描画されません。 |

これらのキーワードはハイフンで組み合わせることができ、組み合わせは「または」を意味します。`<touying:handout-presentation>`（`<touying:slides>` と等価）や `<touying:presentation-article>`（プレゼンテーションモードと記事モードで描画され、配布資料モードではスキップされる）などがあります。

`<touying:never>` は例外です。これは空のモード一覧を表すため、`<touying:never-presentation>` はキーワードではなく、通常のラベルです。

まだ削除したくないスライド、例えば下書き、別のバージョン、あるいはすべてのビルドから除外しておきたいセクションを、`#pause` やラベルを含むマークアップをコメントアウトすることなく、一時的に退避するために使用します。

ラベルは見出しにも、スライド全体にも付けることができます。

```typst
== Only in the handout <touying:handout>

#slide[Only in the presentation] <touying:presentation>
```

例：`<touying:hidden>` は、目次スライドの見出しを番号付け、目次、PDFブックマークの対象から外します（スライド自体は引き続き描画されます）。

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

`appendix` 関数は**分母**、つまり総スライド数を表す `utils.last-slide-number` を固定します。そのため、付録のスライドによってフッターに表示される総数が変わることはありません。スライドカウンター `utils.slide-counter` は進み続けるので、付録のフッターには `4 / 2` のように表示されます。

スライド番号自体も進まないようにしたい場合は、代わりに `config-common(freeze-slide-counter: true)` を使用します。

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
