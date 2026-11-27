---
sidebar_position: 1
---

# Simpleテーマ

このテーマはAndreas Kröpelinが作成した [Polylux](https://polylux.dev/book/themes/gallery/simple.html) に由来します。

比較的シンプルなテーマとされており、簡潔なスライドを作成したり、好みの機能を自由に組み込んだりできます。

## 初期化

次のコードで初期化できます。

```typst
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(
  aspect-ratio: "16-9",
  footer: [Simple slides],
)
```

`simple-theme` 関数は次のパラメーターを受け取ります。

- `aspect-ratio`：スライドのアスペクト比です。"16-9" または "4-3" を指定でき、デフォルトは "16-9" です。
- `header`：ヘッダーに表示される内容です。デフォルトは `self => utils.display-current-heading(setting: utils.fit-to-width.with(grow: false, 100%), level: 1, depth: self.slide-level)` です。`self => self.info.title` のような関数を渡すこともできます。
- `header-right`：ヘッダーの右側に表示される内容です。デフォルトは `self => self.info.logo` です。
- `footer`：フッターに表示される内容です。デフォルトは `none` です。`self => self.info.author` のような関数を渡すこともできます。
- `footer-right`：フッターの右側に表示される内容です。デフォルトは `context utils.slide-counter.display() + " / " + utils.last-slide-number` です。
- `primary`：テーマのプライマリカラーです。デフォルトは `aqua.darken(50%)` です。
- `subslide-preamble`：各スライドの本文の前に挿入される内容です。デフォルトは `block(below: 1.5em, text(1.2em, weight: "bold", utils.display-current-heading(level: 2)))` で、つまり現在のスライドにサブセクションのタイトルを追加します。削除するには `none` を渡します。


## スライド関数群

Simpleテーマはさまざまなカスタムスライド関数を提供します。

```typst
#centered-slide(config: (:), setting: body => body, ..args)[
  ...
]
```

内容を中央に配置するスライドです。新しいセクションを開始するには、直接 `= Title` と記述するか、以下の `#new-section-slide` を呼び出します。

---

```typst
#title-slide(config: (:), body)[
  ...
]
```

`centered-slide` と同じです（加えてスライドカウンターを固定します）。これは主にPolyluxの構文との一貫性のために提供されています。

---

```typst
#new-section-slide(config: (:), body)
```

現在の第1レベルの見出しを表示する、中央配置のセクション区切りです。すでに `config-common(new-section-slide-fn: ..)` で登録されているため、通常は `= Title` と記述することで呼び出され、手動で呼び出す必要はありません。

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

ヘッダーとフッターを備えたデフォルトのスライドです。ヘッダーは現在のセクションに対応し、フッターには設定した内容が表示されます。

---

```typst
#focus-slide(foreground: ..., background: ...)[
  ...
]
```

注意を引くために使用します。必要に応じて前景色（デフォルトは `white`）と背景色（デフォルトは `auto`、つまり `self.colors.primary`）を指定できます。

## 発表者ノート

Simpleは独自の `notes` 関数を定義し、`config-common(notes-fn: notes)` で登録しています。そのため、セカンドスクリーンとノート専用ビューのノートパネルにもテーマの配色が適用されます。この関数は `touying-notes` を基に構築されており、独自の実装で上書きできます。

```typst
#show: simple-theme.with(
  config-common(notes-fn: my-notes),
)
```

詳細は[発表者ノート](../tutorials/speaker-notes.md)を参照してください。


## 使用例

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(
  aspect-ratio: "16-9",
  footer: [Simple slides],
)

#title-slide[
  = Keep it simple!
  #v(2em)

  Alpha #footnote[Uni Augsburg] #h(1em)
  Bravo #footnote[Uni Bayreuth] #h(1em)
  Charlie #footnote[Uni Chemnitz] #h(1em)

  July 23
]

== First slide

#lorem(20)

#focus-slide[
  _Focus!_

  This is very important.
]

= Let's start a new section!

== Dynamic slide

Did you know that...

#pause

...you can see the current section at the top of the slide?
```
