---
sidebar_position: 3
---

# Dewdropテーマ

このテーマはZhibo Wangの [BeamerTheme](https://github.com/zbowang/BeamerTheme) に着想を得ており、[OrangeX4](https://github.com/OrangeX4) が変更を加えました。

Dewdropテーマの特徴は洗練されたナビゲーションです。`sidebar` と `mini-slides` の2モードを備えています。

## 初期化

次のコードで初期化できます。

```typst
#import "@preview/touying:0.8.0": *
#import themes.dewdrop: *

#import "@preview/numbly:0.1.0": numbly

#show: dewdrop-theme.with(
  aspect-ratio: "16-9",
  footer: self => self.info.institution,
  navigation: "mini-slides",
  config-info(
    title: [タイトル],
    subtitle: [サブタイトル],
    author: [著者],
    date: datetime.today(),
    institution: [所属],
    contact: [contact\@mail.com],
  ),
)

#title-slide()

#outline-slide()
```

`dewdrop-theme` 関数は次のパラメーターを受け取ります。

- `aspect-ratio`：スライドの縦横比。`"16-9"` または `"4-3"` を指定でき、既定値は `"16-9"` です。
- `navigation`：ナビゲーションバーの形式。`"sidebar"`、`"mini-slides"`、`none` を指定でき、既定値は `"sidebar"` です。
- `sidebar`：サイドバーの設定。既定値は `(width: 10em, filled: false, numbered: false, indent: .5em, short-heading: true)` です。
  - `width`：サイドバーの幅。
  - `filled`：サイドバーの目次を塗りつぶすかどうか。
  - `numbered`：サイドバーの目次に番号を付けるかどうか。
  - `indent`：サイドバーの目次のインデント。
  - `short-heading`：サイドバーで短い形式の見出しを使用するかどうか。
- `mini-slides`：ミニスライドのナビゲーション設定。既定値は `(height: 4em, x: 2em, display-section: false, display-subsection: true, linebreaks: true, short-heading: true)` です。
  - `height`：ミニスライドの高さ。既定値は `4em` です。
  - `x`：ミニスライドの水平方向のパディング。既定値は `2em` です。
  - `display-section`：セクションとサブセクションの間のスライドを表示するかどうか。既定値は `false` です。
  - `display-subsection`：サブセクション内のスライドを表示するかどうか。既定値は `true` です。
  - `linebreaks`：セクションとサブセクションへのリンクの間で改行するかどうか。既定値は `true` です。
  - `short-heading`：短い見出しを使うかどうか。既定値は `true` です。
  - `inline`：点をセクション・サブセクションのラベルと同じ行に置くかどうか。既定値は `false` です。
- `footer`：フッターに表示する内容。既定値は `none` で、`self => self.info.author` のような関数で変更できます。
- `footer-right`：フッター右側の内容。既定値は `context utils.slide-counter.display() + " / " + utils.last-slide-number` です。
- `primary`：テーマの基本色。既定値は `rgb("#0c4842")` です。
- `alpha`：目次でまだ到達していない見出しを薄く表示する際の透明度。既定値は `60%` です。
- `subslide-preamble`：各サブスライドの本文の前に挿入する内容。既定値は `self => block(text(1.2em, weight: "bold", fill: self.colors.primary, utils.display-current-heading(depth: self.slide-level, style: auto)))` で、現在のスライドレベルの見出しを表示します。除去するには `none` を指定します。

さらにDewdropテーマは強調用の `#alert[..]` 関数を提供します。`#show strong: alert` と組み合わせて使用できます。

## 配色

Dewdropテーマの既定の配色は次のとおりです。

```typc
config-colors(
  neutral-darkest: rgb("#000000"),
  neutral-dark: rgb("#202020"),
  neutral-light: rgb("#f3f3f3"),
  neutral-lightest: rgb("#ffffff"),
  primary: primary,
)
```

`config-colors()` 関数で配色を変更できます。スライドの雰囲気やブランドのガイドラインに合わせて調整できます。


## スライド関数群

Dewdropテーマはさまざまな独自スライド関数を提供します。

```typst
#title-slide(config: (:), extra: none, ..args)
```

`title-slide` は `self.info` から表示情報を取得します。`extra` パラメーターで追加情報も指定できます。

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

ナビゲーションとフッターのある通常のスライドです。フッターには設定した内容が表示されます。

---

```typst
#outline-slide(config: (:), title: utils.i18n-outline-title, ..args)
```

目次スライドを表示します。`..args` は組み込みの `outline` に渡されます。

目次スライドでは `place(hide(heading(..)))` により不可視の見出しを置くため、スライドヘッダーと発表者ノートのパネルの両方で `utils.display-current-heading` からタイトルを取得できます。

---

```typst
#new-section-slide(config: (:), title: utils.i18n-outline-title, ..args, body)
```

指定したタイトルで新しいセクションを開始し、現在のセクションを強調する進捗付き目次を表示します。`config-common(new-section-slide-fn: ..)` に登録されているため、通常は `= Title` のような見出しによって自動で呼び出されます。

---

```typst
#focus-slide[
  ...
]
```

注意を引くためのスライドです。背景色は `self.colors.primary` です。

## 発表者ノート

Dewdropは独自の `notes` 関数を `config-common(notes-fn: notes)` に登録し、セカンドスクリーンやノート専用表示のパネルにもテーマの配色を適用します。`touying-notes` を基にしており、独自の実装で上書きできます。

```typst
#show: dewdrop-theme.with(
  config-common(notes-fn: my-notes),
)
```

詳しくは[発表者ノート](../tutorials/speaker-notes.md)を参照してください。

## 使用例

```example
#import "@preview/touying:0.8.0": *
#import themes.dewdrop: *

#import "@preview/numbly:0.1.0": numbly

#show: dewdrop-theme.with(
  aspect-ratio: "16-9",
  footer: self => self.info.institution,
  navigation: "mini-slides",
  config-info(
    title: [タイトル],
    subtitle: [サブタイトル],
    author: [著者],
    date: datetime.today(),
    institution: [所属],
    contact: [contact\@mail.com],
  ),
)

#set heading(numbering: numbly("{1}.", default: "1.1"))

#title-slide()

#outline-slide()

= セクションA

== サブセクションA.1

$ x_(n+1) = (x_n + a/x_n) / 2 $

== サブセクションA.2

タイトルはありませんが、*重要な*情報を含むスライドです。

= セクションB

== サブセクションB.1

#lorem(80)

#focus-slide[
  注目してください！
]

== サブセクションB.2

`#pause` を使うと、#pause 内容を後から表示できます。

#pause

このように表示されます。

#meanwhile

また、#pause `#meanwhile` を使って #pause 別の内容を同時に表示できます。

#show: appendix

= 付録

== 付録

現在のスライド番号に注目してください。
```
