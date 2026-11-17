---
sidebar_position: 2
---

# Metropolisテーマ

このテーマはMatthias Vogelgesangの [Metropolis beamer](https://github.com/matze/mtheme) に着想を得ており、[Enivex](https://github.com/Enivex) が変更を加えました。

Metropolisは洗練された、日常的に使いやすいテーマです。最適な表示のためにFira SansとFira Mathのインストールを推奨します。

## 初期化

次のコードで初期化できます。

```typst
#import "@preview/touying:0.8.0": *
#import themes.metropolis: *

#import "@preview/numbly:0.1.0": numbly

#show: metropolis-theme.with(
  aspect-ratio: "16-9",
  footer: self => self.info.institution,
  config-info(
    title: [Title],
    subtitle: [Subtitle],
    author: [Authors],
    date: datetime.today(),
    institution: [Institution],
    contact: [contact\@mail.com],
    logo: emoji.city,
  ),
)

#set heading(numbering: numbly("{1}.", default: "1.1"))

#title-slide()
```

`metropolis-theme` 関数は次のパラメーターを受け取ります。

- `aspect-ratio`：スライドの縦横比。`"16-9"` または `"4-3"` を指定でき、既定値は `"16-9"` です。
- `align`：スライド内の内容の配置。既定値は `horizon` です。
- `header`：ヘッダーの内容。既定では `self => utils.display-current-heading(setting: utils.fit-to-width.with(grow: false, 100%), depth: self.slide-level)` により現在の見出しを幅に収まるよう表示します。`self => self.info.title` などで変更できます。
- `header-right`：ヘッダー右側の内容。既定値は `self => self.info.logo` です。
- `footer`：フッターの内容。既定値は `none` です。たとえば `self => self.info.author` で著者情報を表示できます。
- `footer-right`：フッター右側の内容。既定ではスライド番号と総数（`context utils.slide-counter.display() + " / " + utils.last-slide-number`）を表示します。
- `footer-progress`：スライド下部に進捗バーを表示するかどうか。既定値は `true` です。

Metropolisは文字の強調用に `#alert[..]` を提供します。`#show strong: alert` と組み合わせて使用できます。


## 配色

Metropolisの既定の配色は次のとおりです。

```typc
config-colors(
  primary: rgb("#eb811b"),
  primary-light: rgb("#d6c6b7"),
  secondary: rgb("#23373b"),
  neutral-lightest: rgb("#fafafa"),
  neutral-dark: rgb("#23373b"),
  neutral-darkest: rgb("#23373b"),
)
```

`config-colors()` で配色を変更できます。

## スライド関数群

Metropolisテーマには次の独自スライド関数があります。

```typst
#title-slide(config: (:), extra: none, ..args)
```

`title-slide` は `self.info` から表示情報を取得します。`extra` で追加情報も指定できます。

---

```typst
#slide(
  title: auto,
  align: auto,
  config: (:),
  repeat: auto,
  setting: body => body,
  composer: auto,
)[
  ...
]
```

ヘッダーとフッターを備える通常のスライドです。`title: auto` は現在のスライドレベルの見出しを表示し、`align: auto` はテーマの配置設定（既定値は `horizon`）に従います。フッターはテーマの `footer` と `footer-right` で設定し、`#slide` 自体には `footer` パラメーターがありません。

---

```typst
#outline-slide(
  config: (:),
  level: auto,
  title: [Outline],
  spacing: 2em,
  ..args,
)
```

アウトラインスライドを表示します。`level: auto` は `config-common` のスライドレベルを使用します。`..args` は [`components.custom-progressive-outline`](https://touying-typ.github.io/docs/reference/components/custom-progressive-outline) に渡され、`indent`、`vspace`、`numbered`、`numbering` にはMetropolis向けの既定値があります。

アウトラインスライドは `place(hide(heading(..)))` で不可視の見出しを置き、ヘッダーや発表者ノートのパネルから `utils.display-current-heading` によってタイトルを取得できるようにします。

---

```typst
#focus-slide[
  ...
]
```

注目を集めるスライドです。背景色は `self.colors.neutral-dark` です（Metropolisには `primary-dark` がありません）。

---

```typst
#new-section-slide(config: (:), level: 1, numbered: true, body)
```

指定した見出しで新しいセクションを作成します。`config-common(new-section-slide-fn: ..)` に登録済みなので、通常は `= Title` により自動的に呼ばれます。

## 発表者ノート

Metropolisは独自の `notes` 関数を `config-common(notes-fn: notes)` に登録します。セカンドスクリーンやノート専用表示にテーマの配色が適用されます。`touying-notes` を基にしており、独自実装で上書きできます。

```typst
#show: metropolis-theme.with(
  config-common(notes-fn: my-notes),
)
```

詳しくは[発表者ノート](../tutorials/speaker-notes.md)を参照してください。

## 使用例

```example
#import "@preview/touying:0.8.0": *
#import themes.metropolis: *

#import "@preview/numbly:0.1.0": numbly

#show: metropolis-theme.with(
  aspect-ratio: "16-9",
  footer: self => self.info.institution,
  config-info(
    title: [Title],
    subtitle: [Subtitle],
    author: [Authors],
    date: datetime.today(),
    institution: [Institution],
    contact: [contact\@mail.com],
    logo: emoji.city,
  ),
)

#set heading(numbering: numbly("{1}.", default: "1.1"))

#title-slide()

= Outline <touying:hidden>

#outline(title: none, indent: 1em, depth: 1)

= First Section

---

A slide without a title but with some *important* information.

== A long long long long long long long long long long long long long long long long long long long long long long long long Title

=== sdfsdf

A slide with equation:

$ x_(n+1) = (x_n + a/x_n) / 2 $

#lorem(200)

= Second Section

#focus-slide[
  Wake up!
]

== Simple Animation

We can use `#pause` to #pause display something later.

#meanwhile

Meanwhile, #pause we can also use `#meanwhile` to display other content synchronously.

#speaker-note[
  + This is a speaker note.
  + You won't see it unless you use `config-common(show-notes-on-second-screen: right)`
]

#show: appendix

= Appendix

---

Please pay attention to the current slide number.
```
