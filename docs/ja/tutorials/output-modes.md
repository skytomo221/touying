---
sidebar_position: 10
---

# 出力モード

1つのソースファイルから複数の種類の文書を生成できます。Touyingには次の4つの出力モードがあります。

| モード | 出力内容 |
|---|---|
| `presentation` | アニメーションの各サブスライドを独立したページとして出力するプレゼンテーション。 |
| `handout` | 各スライドのサブスライドを1ページ以上にまとめた配布資料。[配布資料モード](dynamic/handout)を参照。 |
| `slides` | `config-common(handout: ..)` に応じて上記2つを切り替えます。既定値です。 |
| `article` | スライドではなく文章形式の文書を出力します。[記事モード](../integration/article-mode)を参照。 |

## モードの選択

設定で指定する場合：

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
#show: simple-theme.with(
  config-common(export-mode: "article"),
)

== A セクション

スライドではなく記事としてコンパイルされます。
```

コマンドラインから指定した場合は、ファイル内の設定より優先されます。

```bash
typst compile talk.typ deck.pdf
typst compile --input export-mode=handout talk.typ handout.pdf
typst compile --input export-mode=article talk.typ paper.pdf
```

## 特定のモードでのみ表示する内容

次の4つのマーカーを使うと、指定したモード以外で内容を非表示にできます。

| マーカー | 表示されるモード |
|---|---|
| `#presentation-only[..]` | `presentation` のみ |
| `#handout-only[..]` | `handout` のみ |
| `#slides-only[..]` | スライド系の2モード（記事では非表示） |
| `#article-only[..]` | `article` のみ |

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
== 各モードで残る内容

#presentation-only[_Live demo here, watch the screen._]

#handout-only[The demo showed three cases.]

#slides-only[Visible in both slide modes, never in the article.]

#article-only[A fuller derivation than the talk had time for.]
```

対象外のモードではマーカー内の内容が完全に削除され、余白も残りません。見出し、`#pagebreak()`、`---` などの改ページ要素を含めた場合も、表示されるモードではマーカーがない場合と同じように動作します。

### マーカーの配置場所

マーカーはスライドの本文内にもスライド関数の外側にも配置できます。違いは適用範囲です。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide[a #slides-only[b] c]

#slides-only(slide[a b c])
```

1つ目は記事から `b` だけを除外し、`a` と `c` を残します。2つ目はスライド全体を除外します。

これはすべてのスライド関数で同様です。`#slides-only(title-slide[])` ならタイトルスライド全体が記事から除外され、`#title-slide[#slides-only[..]]` なら該当部分だけが除外されます。

### スライドを文章に置き換える

`#article-text[..]` は `#article-only[..]` と異なり、記述されたスライド全体を記事用の文章に置き換えます。元のスライド内容は記事に出力されません。各スライドに1つ配置できます。スライドの範囲は `slide-level` 以下の見出しから、次の見出しまたは `#pagebreak()` までです。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme.with(config-common(export-mode: "article"))
== なぜ重要なのか

- terse
- bullet
- points

#article-text[
  In the article this paragraph takes the place of the bullet points above.
]
```

発表用と読み物用を兼ねる文書ではこの方法が便利です。詳しくは[記事モード](../integration/article-mode)を参照してください。

## ラベルマーカー

上記のマーカーは内容を引数に取りますが、見出しにラベルを付ける方法もあります。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
== 常に表示される内容

== 配布資料向けの詳しい説明 <touying:handout>

== ライブデモ <touying:presentation>
```

`<touying:slides>` や `<touying:article>` などは対応する関数と同じように動作し、キーワードはハイフンで組み合わせられます。詳しくは[セクションとラベル](sections)を参照してください。
`<touying:never>` は、どの出力にも含めないセクションを指定するマーカーです。
