---
sidebar_position: 10
---

# 出力モード

1つのソースファイルから複数の文書を生成できます。Touyingではこれらを出力モードと呼び、次の4つがあります。

| モード | 得られるもの |
|---|---|
| `presentation` | アニメーションのすべてのサブスライドがそれぞれ独立したページとなるスライド一式。 |
| `handout` | 同じスライド一式で、各スライドのサブスライドを1ページ以上にまとめたもの。[配布資料モード](dynamic/handout)を参照してください。 |
| `slides` | 上記2つのいずれかを `config-common(handout: ..)` で選択します。これがデフォルトです。 |
| `article` | スライド一式ではなく、文章が連続する文書。[記事モード](../integration/article-mode)を参照してください。 |

## モードの選択

設定で指定します。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
#show: simple-theme.with(
  config-common(export-mode: "article"),
)

== A Section

Compiled as an article rather than as a deck.
```

または、コマンドラインで指定します。この場合はファイル内の設定を上書きします。

```bash
typst compile talk.typ deck.pdf
typst compile --input export-mode=handout talk.typ handout.pdf
typst compile --input export-mode=article talk.typ paper.pdf
```

## 特定のモードでのみ表示する内容

4つのマーカーを使って、ある内容をそれに適さないモードから除外できます。

| マーカー | 表示されるモード |
|---|---|
| `#presentation-only[..]` | プレゼンテーションモードのみ |
| `#handout-only[..]` | 配布資料モードのみ |
| `#slides-only[..]` | 両方のスライドモード。記事には表示されません。 |
| `#article-only[..]` | 記事モードのみ |

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
== What Each Mode Keeps

#presentation-only[_Live demo here, watch the screen._]

#handout-only[The demo showed three cases.]

#slides-only[Visible in both slide modes, never in the article.]

#article-only[A fuller derivation than the talk had time for.]
```

マーカーの内容が不要な場合、その内容は完全に取り除かれ、領域も確保されません。本文には、見出し、`#pagebreak()`、単独の `---` といったスライドを分割する要素を含めることができます。その内容が表示されるモードでは、これらはマーカーが存在しない場合とまったく同じように動作します。

### マーカーの配置場所

マーカーはスライドの本文の内部でも、呼び出しを囲む形でも機能します。違いは適用範囲です。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide[a #slides-only[b] c]

#slides-only(slide[a b c])
```

1つ目は記事から `b` を除外し、`a` と `c` を残します。2つ目はスライド全体を除外します。

これはすべてのスライド関数に当てはまります。`#slides-only(title-slide[])` は記事からタイトルスライド全体を除外しますが、`#title-slide[#slides-only[..]]` はその部分だけを取り除きます。

### スライドを文章に置き換える

`#article-text[..]` は `#article-only[..]` よりも踏み込んだ機能です。記述されたスライドの代わりになるため、スライド自体の内容は記事に出力されません。どこに配置してもスライド全体を対象とし、スライドごとに1つあります。ここでスライドとは、`slide-level` より深くない見出しと、その下の次の見出しまでのすべての内容、または `#pagebreak()` があればそこまでの内容です。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme.with(config-common(export-mode: "article"))
== Why This Matters

- terse
- bullet
- points

#article-text[
  In the article this paragraph takes the place of the bullet points above.
]
```

これは、文書としても読める必要がある発表を記述する一般的な方法です。残りの詳細は[記事モード](../integration/article-mode)を参照してください。

## ラベルマーカー

上のマーカーは内容を受け取ります。代わりに見出しにラベルマーカーを使用することもできます。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
== Always Here

== Extra Detail for the Handout <touying:handout>

== Live Demo <touying:presentation>
```

`<touying:slides>`、`<touying:article>` などは関数と同様に機能し、キーワードはハイフンで組み合わせられます。[セクションとラベル](sections)に完全な表があります。
さらに、セクションをどの出力にも含めないようにマークする `<touying:never>` もあります。
