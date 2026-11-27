---
sidebar_position: 3
---

# Dewdropテーマ

このテーマはZhibo Wangの [BeamerTheme](https://github.com/zbowang/BeamerTheme) に着想を得たもので、[OrangeX4](https://github.com/OrangeX4) によって変更が加えられています。

Dewdropテーマは洗練されたデザインのナビゲーションを特徴とし、`sidebar` と `mini-slides` の2つのモードを備えています。

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
    title: [Title],
    subtitle: [Subtitle],
    author: [Authors],
    date: datetime.today(),
    institution: [Institution],
    contact: [contact\@mail.com],
  ),
)

#title-slide()

#outline-slide()
```

`dewdrop-theme` 関数は次のパラメーターを受け取ります。

- `aspect-ratio`：スライドのアスペクト比です。"16-9" または "4-3" を指定でき、デフォルトは "16-9" です。
- `navigation`：ナビゲーションバーのスタイルです。`"sidebar"`、`"mini-slides"`、`none` のいずれかを指定でき、デフォルトは `"sidebar"` です。
- `sidebar`：サイドバーナビゲーションの設定です。デフォルト値は `(width: 10em, filled: false, numbered: false, indent: .5em, short-heading: true)` です。
  - `width`：サイドバーの幅です。
  - `filled`：サイドバー内のアウトラインを塗りつぶすかどうかです。
  - `numbered`：サイドバー内のアウトラインに番号を付けるかどうかです。
  - `indent`：サイドバー内のアウトラインのインデントです。
  - `short-heading`：サイドバーで短い形式の見出しを使用するかどうかです。
- `mini-slides`：ミニスライドナビゲーションの設定です。デフォルト値は `(height: 4em, x: 2em, display-section: false, display-subsection: true, linebreaks: true, short-heading: true)` です。
  - `height`：ミニスライドの高さです。デフォルトは `4em` です。
  - `x`：ミニスライドのx軸方向のパディングです。デフォルトは `2em` です。
  - `display-section`：セクションの後、サブセクションの前にあるスライドを表示するかどうかです。デフォルトは `false` です。
  - `display-subsection`：サブセクション内のスライドを表示するかどうかです。デフォルトは `true` です。
  - `linebreaks`：セクションへのリンクとサブセクションへのリンクの間で改行するかどうかです。デフォルトは `true` です。
  - `short-heading`：短い形式の見出しを使用するかどうかです。デフォルトは `true` です。
  - `inline`：ドットをセクション・サブセクションのラベルと同じ行に配置するかどうかです。デフォルトは `false` です。
- `footer`：スライドのフッターに表示する内容です。デフォルトは `none` です。例えば `self => self.info.author` のような関数でカスタマイズできます。
- `footer-right`：フッターの右側に表示される内容です。デフォルトは `context utils.slide-counter.display() + " / " + utils.last-slide-number` です。
- `primary`：テーマのプライマリカラーです。デフォルトは `rgb("#0c4842")` です。
- `alpha`：アウトライン内でカバーされている（まだ到達していない）見出しの透明度です。デフォルトは `60%` です。
- `subslide-preamble`：各スライドの本文の前に挿入される内容です。デフォルトは `self => block(text(1.2em, weight: "bold", fill: self.colors.primary, utils.display-current-heading(depth: self.slide-level, style: auto)))`、つまり現在のスライドレベルの見出しです。削除するには `none` を渡します。

さらにDewdropテーマは `#alert[..]` 関数を提供します。`#show strong: alert` 構文と組み合わせて強調されたアラートテキストを作成できます。

## 配色

Dewdropテーマではデフォルトで次の配色を使用します。

```typc
config-colors(
  neutral-darkest: rgb("#000000"),
  neutral-dark: rgb("#202020"),
  neutral-light: rgb("#f3f3f3"),
  neutral-lightest: rgb("#ffffff"),
  primary: primary,
)
```

この配色は `config-colors()` 関数を使って変更できます。これにより、スライドのカラーパレットを目指す美的なスタイルや特定のブランドガイドラインに合わせて調整できます。


## スライド関数群

Dewdropテーマはさまざまなカスタムスライド関数を提供します。

```typst
#title-slide(config: (:), extra: none, ..args)
```

`title-slide` は表示する情報を `self.info` から読み取ります。また、`extra` パラメーターを渡して追加情報を表示することもできます。

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

ナビゲーションとフッターを備えたデフォルトのスライドです。フッターには設定した内容が表示されます。

---

```typst
#outline-slide(config: (:), title: utils.i18n-outline-title, ..args)
```

アウトラインスライドを表示します。`..args` は組み込みの `outline` に転送されます。

アウトラインスライドでは `place(hide(heading(..)))` を使って不可視の見出しを配置するため、スライドのヘッダーと発表者ノートのパネルの両方が `utils.display-current-heading` を通してタイトルを取得できます。

---

```typst
#new-section-slide(config: (:), title: utils.i18n-outline-title, ..args, body)
```

指定されたタイトルで新しいセクションを開始し、現在のセクションを強調する進捗付きアウトラインを表示します。すでに `config-common(new-section-slide-fn: ..)` で登録されているため、通常は `= Title` と記述することで呼び出され、手動で呼び出す必要はありません。

---

```typst
#focus-slide[
  ...
]
```

注意を引くために使用します。背景色は `self.colors.primary` に設定されています。

## 発表者ノート

Dewdropは独自の `notes` 関数を定義し、`config-common(notes-fn: notes)` で登録しています。そのため、セカンドスクリーンとノート専用ビューのノートパネルにもテーマの配色が適用されます。この関数は `touying-notes` を基に構築されており、独自の実装で上書きできます。

```typst
#show: dewdrop-theme.with(
  config-common(notes-fn: my-notes),
)
```

詳細は[発表者ノート](../tutorials/speaker-notes.md)を参照してください。

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
    title: [Title],
    subtitle: [Subtitle],
    author: [Authors],
    date: datetime.today(),
    institution: [Institution],
    contact: [contact\@mail.com],
  ),
)

#set heading(numbering: numbly("{1}.", default: "1.1"))

#title-slide()

#outline-slide()

= Section A

== Subsection A.1

$ x_(n+1) = (x_n + a/x_n) / 2 $

== Subsection A.2

A slide without a title but with *important* infos

= Section B

== Subsection B.1

#lorem(80)

#focus-slide[
  Wake up!
]

== Subsection B.2

We can use `#pause` to #pause display something later.

#pause

Just like this.

#meanwhile

Meanwhile, #pause we can also use `#meanwhile` to #pause display other content synchronously.

#show: appendix

= Appendix

== Appendix

Please pay attention to the current slide number.
```
