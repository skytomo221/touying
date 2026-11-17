---
sidebar_position: 6
---

# Stargazerテーマ

Stargazerテーマは、[Coekjan](https://github.com/Coekjan/) が [touying-buaa](https://github.com/Coekjan/touying-buaa) プロジェクト向けに作成した、美しく汎用性の高い日常利用に適したテーマです。

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

- `aspect-ratio`：スライドの縦横比。`"16-9"` または `"4-3"` を指定できます。既定値は `"16-9"` です。
- `align`：スライド内の配置。既定値は `horizon` です。
- `alpha`：アウトラインで未到達の見出しを隠す際の不透明度。既定値は `20%` です。
- `title`：ヘッダーに表示する内容。既定値は `self => utils.display-current-heading(depth: self.slide-level)` です。`self => self.info.title` のような関数も指定できます。
- `header-right`：ヘッダーのナビゲーションバー右側の内容。既定値は `self => self.info.logo` です。
- `progress-bar`：スライド下部に進捗バーを表示するかどうか。既定値は `true` です。
- `footer-columns`：フッターの4列の幅。既定値は `(25%, 25%, 1fr, 5em)` です。
- `footer-a`：第1列。既定値は `self => self.info.author` です。
- `footer-b`：第2列。既定値は `self => utils.display-info-date(self)` です。
- `footer-c`：第3列。既定では短いタイトルがあればそれを、なければ通常のタイトルを表示します。
- `footer-d`：第4列。既定では現在のスライド番号と総数を表示します。

## 配色

Stargazerの既定の配色は次のとおりです。

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

`config-colors()` で配色を変更できます。

## スライド関数群

Stargazerテーマは、次の独自スライド関数を提供します。

```typst
#title-slide(config: (:), extra: none, ..args)
```

`title-slide` は `self.info` から表示情報を読み取ります。`extra` で追加情報も指定できます。

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

タイトルとフッターを備える標準のスライド関数です。`title`、`header`、`footer`、`align` の既定値はすべて `auto` であり、テーマ側の設定に従います。`auto` 以外を指定すると、そのスライドに限って対応する `self.store` の値を上書きします。

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

目次スライドを追加します。`..args` は [`components.custom-progressive-outline`](https://touying-typ.github.io/docs/reference/components/custom-progressive-outline) に渡されます。

アウトラインスライドは `place(hide(heading(..)))` で不可視の見出しを配置し、ヘッダーや発表者ノートのパネルから `utils.display-current-heading` でタイトルを取得できるようにします。`self.store.title` は上書きしません。

---

```typst
#focus-slide[
  ...
]
```

聴衆の注意を引くスライドです。背景色は `self.colors.primary` です。

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

指定した見出しで新しいセクションを開始します。内部では `outline-slide` を呼び出します。`config-common(new-section-slide-fn: ..)` に登録済みで、通常は `= Title` により自動で呼び出されます。

---

```typst
#ending-slide(config: (:), title: none, body)
```

終了スライドに使います。`title` を指定すると `self.colors.tertiary` で塗りつぶしたブロックに表示されます。同時に不可視の見出しを置き、ヘッダーや発表者ノートのパネルからタイトルを取得できるようにします。

---

```typst
#tblock(title: none, it)
```

Stargazer独自の定理ブロックです。タイトルバーに `self.colors.primary-dark`、本文に明るくした `self.colors.primary` を使います。`touying-fn-wrapper-raw` を基盤とするため、`#pause` や `#uncover` と組み合わせても正常に動作します。

## 発表者ノート

Stargazerは独自の `notes` 関数を `config-common(notes-fn: notes)` に登録します。セカンドスクリーンやノート専用表示にもテーマの配色を適用します。`touying-notes` を基にしており、独自実装で上書きできます。

```typst
#show: stargazer-theme.with(
  config-common(notes-fn: my-notes),
)
```

詳しくは[発表者ノート](../tutorials/speaker-notes.md)を参照してください。

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
