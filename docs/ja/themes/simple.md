---
sidebar_position: 1
---

# Simpleテーマ

このテーマはAndreas Kröpelinが作成した [Polylux](https://polylux.dev/book/themes/gallery/simple.html) のテーマを基にしています。

比較的単純なテーマなので、簡潔なスライドを作成し、好みの機能を自由に組み込めます。

## 初期化

次のコードで初期化できます。

```typst
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(
  aspect-ratio: "16-9",
  footer: [シンプルなスライド],
)
```

`simple-theme` 関数は次のパラメーターを受け取ります。

- `aspect-ratio`：スライドの縦横比。`"16-9"` または `"4-3"` を指定でき、既定値は `"16-9"` です。
- `header`：ヘッダーに表示する内容。既定値は `self => utils.display-current-heading(setting: utils.fit-to-width.with(grow: false, 100%), level: 1, depth: self.slide-level)` です。`self => self.info.title` のような関数も指定できます。
- `header-right`：ヘッダー右側の内容。既定値は `self => self.info.logo` です。
- `footer`：フッターの内容。既定値は `none` です。`self => self.info.author` のような関数も指定できます。
- `footer-right`：フッター右側の内容。既定値は `context utils.slide-counter.display() + " / " + utils.last-slide-number` です。
- `primary`：テーマの基本色。既定値は `aqua.darken(50%)` です。
- `subslide-preamble`：各スライドの本文の前に挿入する内容。既定値は `block(below: 1.5em, text(1.2em, weight: "bold", utils.display-current-heading(level: 2)))` で、サブセクションの見出しを表示します。無効にするには `none` を指定します。


## スライド関数群

Simpleテーマはさまざまな独自スライド関数を提供します。

```typst
#centered-slide(config: (:), setting: body => body, ..args)[
  ...
]
```

内容を中央に配置するスライドです。新しいセクションを始めるには `= Title` と書くか、後述の `#new-section-slide` を呼び出します。

---

```typst
#title-slide(config: (:), body)[
  ...
]
```

`centered-slide` と同様ですが、スライドカウンターも固定します。主にPolyluxの構文との一貫性を保つために提供されています。

---

```typst
#new-section-slide(config: (:), body)
```

現在の第1レベルの見出しを中央に表示するセクション区切りです。`config-common(new-section-slide-fn: ..)` に登録されているため、通常は `= Title` により自動的に呼び出されます。

---

```typst
#slide(
  config: (:),
  repeat: auto,
  setting: body => body,
  composer: auto,
)[
  ...
]
```

ヘッダーとフッターを備える通常のスライドです。ヘッダーは現在のセクション、フッターは指定した内容を表示します。

---

```typst
#focus-slide(foreground: ..., background: ...)[
  ...
]
```

注目を集めるためのスライドです。前景色（既定値は `white`）と背景色（既定値は `auto`、つまり `self.colors.primary`）を指定できます。

## 発表者ノート

Simpleテーマは独自の `notes` 関数を `config-common(notes-fn: notes)` に登録し、セカンドスクリーンやノート専用表示にもテーマの配色を適用します。`touying-notes` を基にしており、独自の実装で上書きできます。

```typst
#show: simple-theme.with(
  config-common(notes-fn: my-notes),
)
```

詳しくは[発表者ノート](../tutorials/speaker-notes.md)を参照してください。


## 使用例

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(
  aspect-ratio: "16-9",
  footer: [シンプルなスライド],
)

#title-slide[
  = シンプルに作ろう！
  #v(2em)

  Alpha #footnote[アウクスブルク大学] #h(1em)
  Bravo #footnote[バイロイト大学] #h(1em)
  Charlie #footnote[ケムニッツ工科大学] #h(1em)

  July 23
]

== First slide

#lorem(20)

#focus-slide[
  _Focus!_

  これはとても重要です。
]

= 新しいセクションを始めましょう！

== Dynamic slide

Did you know that...

#pause

...you can see the current section at the top of the slide?
```
