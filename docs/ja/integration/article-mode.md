---
sidebar_position: 7
---

# 記事モード

記事モードでは、同じソースファイルをスライドではなく連続した文章の文書として出力します。

```bash
typst compile talk.typ talk.pdf                            # スライド出力
typst compile --input export-mode=article talk.typ talk.pdf   # 記事出力
```

`--input export-mode=article` はファイル内の指定より優先されるため、ソースを書き換えずに両方の出力を生成できます。ファイル内でモードを固定したい場合は、設定に指定します。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
#show: simple-theme.with(
  config-common(export-mode: "article"),
)

== A Section

このページにはスライド用ではなく記事用のテーマが適用されます。
```

`export-mode` には `"slides"`、`"presentation"`、`"handout"`、`"article"` を指定できます。各モードの関係は[出力モード](../tutorials/output-modes)を参照してください。

## 何が変わるか

- **セクション間では改ページしません。** 内容は連続し、見出しはスライドではなくセクションの開始を表します。
- **アニメーションはまとめられます。** 各スライドは最後のサブスライドの状態で描画されます。`#pause` や `#uncover` を使った内容も最終状態になります。
- **レイアウトとスタイルは記事テーマが決定します。** スライドテーマのヘッダー、フッター、16:9のページ設定などは使われません。
- **レイアウトは線形化されます。** 配置のためだけのコンテナーは文章の流れに展開され、スライドのレイアウトは維持しません。詳しくは[レイアウトの線形化](#linearized-layout)を参照してください。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
#show: simple-theme.with(
  config-common(export-mode: "article"),
)

== A Section

Ordinary content. #pause This appears on a second subslide in the deck, and inline here.
```

## 記事テーマの選択

Touyingには既定で使われるシンプルな記事テーマが含まれています。本文を受け取るテーマなら、Universeの文書テーマなども使えます。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
#import "@preview/arkheion:0.1.0": arkheion

#show: simple-theme.with(
  config-common(export-mode: "article", article-theme: arkheion),
)

== A Section

Rendered by arkheion rather than by touying's own article theme.
```

テーマは記事全体に適用する関数です。指定時に `.with(..)` で設定してください。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
#import themes.article: article-theme

#show: simple-theme.with(
  config-common(
    export-mode: "article",
    article-theme: article-theme.with(numbering: "1.1"),
  ),
)

= あるパート

== A Section

Numbered by the article theme.
```

### スライドのメタデータを渡す

記事テーマは独自のタイトルや著者情報を必要とすることが多くあります。`config-article(available-fields: ..)` はTouyingの設定項目を記事テーマのパラメーターへ対応付けるため、`config-info` などに一度記述すれば再利用できます。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
#import "@preview/arkheion:0.1.0": arkheion

#show: simple-theme.with(
  config-info(title: [投影], author: [A. 著者]),
  config-common(export-mode: "article", article-theme: arkheion),
  config-article(available-fields: (title: "info.title")),
)

== A Section

上のタイトルは `config-info` から取得されました。
```

キーにはテーマのパラメーター名、値にはTouyingの設定へのパスを指定します。テーマが受け付け、型も一致するフィールドだけを指定してください。たとえばarkheionの `authors` は辞書の配列を要求するため、`config-info` の `author` を直接渡すと `element text has no method map` で失敗します。先にテーマの関数シグネチャを確認してください。

テーマにタイトルブロックがない場合は、`config-article(title-block-fn: ..)` にタイトルを返す関数を渡すか、最初のスライドの前に `#article-only[..]` で独自のタイトルを書きます。

## 両方の出力に対応した記述

出力先によって内容を少し変更したい場合は、3つの関数で出力内容を選択できます。詳しくは[出力モード](../tutorials/output-modes)を参照してください。

- `#article-only[..]` は記事にのみ表示する内容を追加します。見出しや `---` などのスライド区切りも内部に記述できます。
- `#slides-only[..]` はスライド出力の場合だけ内容を表示します。
- `#article-text[..]` は対象スライドを文章で**置き換えます**。1スライドに1つだけ指定できます。

特に便利なのが `#article-text` です。スライドで読みやすい箇条書きが文書では読みにくい場合、文章版も隣に記述し、出力先に応じて使い分けられます。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
#show: simple-theme.with(
  config-common(export-mode: "article"),
)

== Why This Matters

- terse
- bullet
- points

#article-text[
  In the article this paragraph stands in for the bullet points above, which
  never reach the page.
]
```

これは対象スライド全体を置き換えるため、見出しから生成したスライドでも明示的な `#slide[..]` でも同様に動作します。記述位置は対象内容の前、後、途中のどこでも構いません。各スライドで使用できる `#article-text[..]` は1つだけです。

ここでのスライドは、`slide-level` 以下の見出し（既定では `=` と `==`）から始まる範囲です。その下の `===` もスライドの内容とみなされ、まとめて置き換えられます。単独の `---` はスライドの区切りとして記事では無視されます。一方、`#pagebreak()` は記事でも改ページされるためスライドの境界を分けます。したがって `---` の代わりに `#pagebreak()` で区切れば、各部分に `article-text` を配置できます。`#article-text[..]` や `#article-only[..]` の内部では、`---` はスライドを分割せずそのまま残ります。

## アニメーション化した内容の再利用

アニメーション化した図は複数のサブスライドを持ちますが、記事では最後の状態だけが描画されます。途中の段階が説明に重要なら、`#touying-recall` で選択した段階を文章中に再表示できます。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme.with(config-common(export-mode: "article"))
#import "@preview/cetz:0.4.2"
#let canvas = touying-reducer.with(
  reduce: cetz.canvas,
  cover: cetz.draw.hide.with(bounds: true),
)

== The Construction
#canvas(label: "fig", {
  import cetz.draw: *
  rect((0, 0), (4, 3))
  (pause,)
  circle((2, 1.5), radius: 0.8)
}, length: 20pt)

#article-text[
  最初に枠を固定します：
  #touying-recall(<fig>, subslides: 1)

  Then the inscribed circle follows:
  #touying-recall(<fig>, subslides: 2)
]
```

`subslides` で段階を選択します。呼び出しがスライドの先頭にない場合は、外側の内容の流れを基準に番号が数えられるため、`base:` を指定してください。
詳しくは各関数のドキュメントを参照してください。同様の `touying-render` は内容を受け取り、指定したサブスライドで描画します。

## レイアウトの線形化

記事は1列の文章なので、スライド内の配置だけを目的とするコンテナーは文章の流れに展開されます。スライドのcomposer、`#columns(..)`、`components.side-by-side[..][..]` は既定で線形化されます。

`table` や `grid` は構造自体が意味を持つ場合があります。Touyingは既定で**宣言されたヘッダーまたはフッター**を構造的な意味の証拠とみなし、ある場合は構造を維持し、ない場合は配置用の要素として線形化します。`components.cols` と `side-by-side` は内部でグリッドを構築しますがヘッダーを宣言しないため、線形化されます。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme.with(config-common(export-mode: "article"))
== Two Ways To Use A Table

#components.side-by-side[Left half.][Right half.]

#table(
  columns: 2,
  table.header([要素], [維持]),
  [with a header], [yes],
  [without one], [no],
)
```

`config-article(linearize: ..)` で動作を変更できます。`auto` は前述の既定動作、`true` と `false` は強制指定です。辞書を使うと `"columns"`、`"grid"`、`"table"` ごとに設定できます。`"columns"` はそれ自体に構造的な意味がないため、`auto` は `true` と同じです。

図の本文はキャプションによってレイアウトに意味が生じるため、線形化されません。

```typst
#show: simple-theme.with(
  config-article(linearize: (table: false, grid: auto, columns: true)),
)
```

`#article-linearize[..]` と `#article-keep-layout[..]` で要素ごとにグローバル設定を上書きできます。

```typst
#article-linearize[#components.side-by-side[left][right]]

#article-keep-layout[#table(columns: 2, [a], [b]) <tab:x>]
```
`#article-only` と `#article-text` の内部は記述されたまま保持されるため、その内部で上記のマーカーを使うとエラーになります。

## ブロックを横に回り込ませる

読みやすさを改善するため、`config-article(wrap: ..)` で画像を片側に回り込ませることができます。

回り込みを行わない図と表は親セクションの下部中央に配置されます。文章の流れの中に配置したい場合は `article-only` または `article-text` を使用してください。それ以外のブロック（画像を含む）は線形化後の位置に残ります。

```typst
#show: simple-theme.with(
  config-common(export-mode: "article"),
  config-article(wrap: (
    width: 50%,     // 回り込み要素が占める本文の幅
    align: right,   // 配置する側
    image: true,    // 通常の画像。既定で有効
    table: false,
  )),
)
```

`width` と `align` をグローバルに指定し、`table`、`image`、`figure` などの要素関数ごとに回り込みの有無を設定できます。

トップレベルの短縮形として、`wrap: false` はすべての回り込みを無効にし、`wrap: true` は対象となる要素すべてを既定の幅と配置で回り込ませます。

グローバルな既定値を要素関数ごとに上書きすることもできます。

```typst
config-article(wrap: (
  image: (align: right, width: 30%),
  table: (align: left, width: 45%),
))
```

より複雑な要素選択も可能ですが、ここではTypstのセレクターを使用できないため、代わりに述語を指定する必要があります。

```typst
config-article(wrap: (
  image: true,
  overrides: (
    // 画像を含む図は回り込み、表を含む図は回り込まない
    (target: el => el.func() == figure and el.body.func() == image,
     align: left, width: 35%),
  ),
))
```

述語が参照するのは内容の木構造です。この段階では図の `kind` はまだ `auto` のため、`el.kind` ではなく `el.body.func()` を調べます。

[CeTZ](https://typst.app/universe/package/cetz) や [Fletcher](https://typst.app/universe/package/fletcher) で描画した図は、記事が回り込み要素を収集する時点ではコンテキスト内容になっています。そのため `touying-reducer` は描画関数（例：`cetz.canvas`）で出力をマークし、`graphic-marker-of` は対応する述語を生成します。

```typst
#import "@preview/cetz:0.4.2"

config-article(wrap: (
  overrides: (
    (target: graphic-marker-of(cetz.canvas), align: left, width: 40%),
  ),
))
```

アニメーションのない図はreducerを通らないため、`#graphic-marker(cetz.canvas, ..)` で自分でマークしてください。同じ述語で検出できます。マークは不可視で、どちらの出力でも描画前に取り除かれます。

回り込みには [meander](https://typst.app/universe/package/meander) を使用し、実際に回り込ませる要素を持つセクションだけで読み込まれます。

## 知っておくべきこと

**タイトルスライドも本文に現れます。** `#title-slide(..)` も通常のスライドと同様に記事へ出力されるため、タイトルが重複する場合があります。出力から除外するには呼び出しをラップしてください。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme.with(config-common(export-mode: "article"))
#slides-only(title-slide[])

== A Section

No duplicated title block above this.
```

**マークは探索可能な位置に置く必要があります。** `#pause` や `#article-text` などはレイアウトより前に文書を走査して解決されます。`#context` ブロックや測定対象のコンテナー内部に置くと到達できず、`Unsupported mark` で失敗します。自動でサブスライド数を数えるアニメーション関数やほかの `#context` 式で囲まないでください。

**発表者ノートは表示されません。** `#speaker-note[..]` は発表者ビュー用です。記事の補足説明には `#article-only[..]` を使います。
