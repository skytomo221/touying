---
sidebar_position: 6
---

# Stargazerテーマ

Stargazerテーマは、もともと [Coekjan](https://github.com/Coekjan/) が [touying-buaa](https://github.com/Coekjan/touying-buaa) プロジェクト向けに作成した、美しく汎用性が高い、日常的な利用に適したテーマです。

## 初期化

次のコードでテーマを初期化できます。

```typst
#import "@preview/touying:0.8.0": *
#import themes.stargazer: *

#import "@preview/numbly:0.1.0": numbly

#show: stargazer-theme.with(
  aspect-ratio: "16-9",
  config-info(
    title: [Stargazer in Touying: Customize Your Slide Title Here],
    subtitle: [Customize Your Slide Subtitle Here],
    author: [Authors],
    date: datetime.today(),
    institution: [Institution],
    contact: [contact\@mail.com],
    logo: emoji.school,
  ),
)

#set heading(numbering: numbly("{1}.", default: "1.1"))

#title-slide()

#outline-slide()
```

`stargazer-theme` は次のパラメーターを受け取ります。

- `aspect-ratio`：スライドのアスペクト比です。"16-9" または "4-3" のいずれかで、デフォルトは "16-9" です。
- `align`：スライドの配置です。デフォルトは `horizon` です。
- `alpha`：アウトライン内でカバーされている（まだ到達していない）見出しの透明度です。デフォルトは `20%` です。
- `title`：ヘッダーに表示される内容です。デフォルトは `self => utils.display-current-heading(depth: self.slide-level)` です。または `self => self.info.title` のような関数を渡すこともできます。
- `header-right`：ヘッダーのナビゲーションバーの右側に表示される内容です。デフォルトは `self => self.info.logo` です。
- `progress-bar`：スライドの下部に進捗バーを表示するかどうかです。デフォルトは `true` です。
- `footer-columns`：フッターの4列の幅です。デフォルトは `(25%, 25%, 1fr, 5em)` です。
- `footer-a`：第1列です。デフォルトは `self => self.info.author` です。
- `footer-b`：第2列です。デフォルトは `self => utils.display-info-date(self)` です。
- `footer-c`：第3列です。デフォルトは `self => if self.info.short-title == auto { self.info.title } else { self.info.short-title }` です。
- `footer-d`：第4列です。デフォルトは `context utils.slide-counter.display() + " / " + utils.last-slide-number` です。

## 配色

Stargazerテーマではデフォルトで次の配色を使用します。

```typc
config-colors(
  primary: rgb("#005bac"),
  primary-dark: rgb("#004078"),
  secondary: rgb("#ffffff"),
  tertiary: rgb("#005bac"),
  neutral-lightest: rgb("#ffffff"),
  neutral-darkest: rgb("#000000"),
)
```

この配色は `config-colors()` を使用して変更できます。

## スライド関数群

Stargazerテーマはさまざまなカスタムスライド関数を提供します。

```typst
#title-slide(config: (:), extra: none, ..args)
```

`title-slide` は表示する情報を `self.info` から読み取ります。また、追加情報のために `extra` パラメーターを渡すこともできます。

---

```typst
#slide(
  title: auto,
  header: auto,
  footer: auto,
  align: auto,
  config: (:),
  repeat: auto,
  setting: body => body,
  composer: auto,
)[
  ...
]
```

タイトルとフッターを備えた標準のスライド関数です。`title`、`header`、`footer`、`align` はすべてデフォルトが `auto` であり、テーマレベルの設定に従います。`auto` 以外の値を指定すると、その1枚のスライドについてのみ、対応する `self.store` フィールドを上書きします。

---

```typst
#outline-slide(
  config: (:),
  title: utils.i18n-outline-title,
  numbered: true,
  level: none,
  ..args,
)
```

目次スライドを追加するために使用します。`..args` は [`components.custom-progressive-outline`](https://touying-typ.github.io/docs/reference/components/custom-progressive-outline) に転送されます。

アウトラインスライドでは `place(hide(heading(..)))` を使って不可視の見出しを配置するため、スライドのヘッダーと発表者ノートのパネルの両方が `utils.display-current-heading` を通してタイトルを取得できます。以前とは異なり、`self.store.title` を上書きしません。

---

```typst
#focus-slide[
  ...
]
```

聴衆の注意を引くために使用します。背景色は `self.colors.primary` です。

---

```typst
#new-section-slide(
  config: (:),
  title: utils.i18n-outline-title,
  level: 1,
  numbered: true,
  ..args,
  body,
)
```

指定されたタイトルで新しいセクションを開始します。これは `outline-slide` に処理を委譲することで実装されています。すでに `config-common(new-section-slide-fn: ..)` で登録されているため、通常は `= Title` と記述することで呼び出され、手動で呼び出す必要はありません。

---

```typst
#ending-slide(config: (:), title: none, body)
```

終了スライドに使用します。`title` が指定された場合は `self.colors.tertiary` で塗りつぶされたブロック内に表示され、その傍らには不可視の見出しが配置されるため、スライドのヘッダーと発表者ノートのパネルがタイトルを取得できます。

---

```typst
#tblock(title: none, it)
```

Stargazer独自の定理ブロックです。タイトルバーには `self.colors.primary-dark` を使用し、本文には明るくした `self.colors.primary` を使用します。`touying-fn-wrapper-raw` を基に構築されているため、`#pause` や `#uncover` などのアニメーション関数と組み合わせても通常どおりに動作します。

## 発表者ノート

Stargazerは独自の `notes` 関数を定義し、`config-common(notes-fn: notes)` で登録しています。そのため、セカンドスクリーンとノート専用ビューのノートパネルにもテーマの配色が適用されます。この関数は `touying-notes` を基に構築されており、独自の実装で上書きできます。

```typst
#show: stargazer-theme.with(
  config-common(notes-fn: my-notes),
)
```

詳細は[発表者ノート](../tutorials/speaker-notes.md)を参照してください。

## 使用例

```example
#import "@preview/touying:0.8.0": *
#import themes.stargazer: *

#import "@preview/numbly:0.1.0": numbly

#show: stargazer-theme.with(
  aspect-ratio: "16-9",
  config-info(
    title: [Stargazer in Touying: Customize Your Slide Title Here],
    subtitle: [Customize Your Slide Subtitle Here],
    author: [Authors],
    date: datetime.today(),
    institution: [Institution],
    contact: [contact\@mail.com],
    logo: emoji.school,
  ),
)

#set heading(numbering: numbly("{1}.", default: "1.1"))

#title-slide()

#outline-slide()

= Section A

== Subsection A.1

#tblock(title: [Theorem])[
  A simple theorem.

  $ x_(n+1) = (x_n + a / x_n) / 2 $
]

== Subsection A.2

A slide without a title but with *important* information.

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
