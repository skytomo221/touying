---
sidebar_position: 2
---

# Metropolisテーマ

このテーマはMatthias Vogelgesangの [Metropolis beamer](https://github.com/matze/mtheme) テーマに着想を得たもので、[Enivex](https://github.com/Enivex) によって変更が加えられています。

Metropolisテーマは洗練されており、日常的な利用に適しています。最適な結果を得るために、コンピューターにFira SansとFira Mathフォントをインストールしておくことを推奨します。

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

- `aspect-ratio`：スライドのアスペクト比です。"16-9" または "4-3" を指定でき、デフォルトは "16-9" です。
- `align`：スライド内の内容の配置です。デフォルトは `horizon` です。
- `header`：スライドのヘッダーに表示される内容です。デフォルトは `self => utils.display-current-heading(setting: utils.fit-to-width.with(grow: false, 100%), depth: self.slide-level)` で、現在の見出しを幅に収まるように表示します。代わりに `self => self.info.title` のような関数を指定してヘッダーの内容をカスタマイズすることもできます。
- `header-right`：ヘッダーの右側に表示される内容です。デフォルトは `self => self.info.logo` です。
- `footer`：スライドのフッターに表示される内容です。デフォルトは `none` です。関数を使ってカスタマイズすることができます。例えば著者情報を表示するには `self => self.info.author` を使用します。
- `footer-right`：フッターの右側に表示される内容です。デフォルトではスライド番号とスライドの総数（`context utils.slide-counter.display() + " / " + utils.last-slide-number`）を表示します。
- `footer-progress`：スライドの下部に進捗バーを表示するかどうかを示す真偽値です。デフォルトは `true` です。

さらにMetropolisテーマは `#alert[..]` 関数を提供します。`#show strong: alert` 構文と組み合わせて、スライド内のテキストを強調できます。


## 配色

Metropolisは次のデフォルト配色を使用します。

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

この配色は `config-colors()` を使用して変更できます。

## スライド関数群

Metropolisテーマはさまざまなカスタムスライド関数を提供します。

```typst
#title-slide(config: (:), extra: none, ..args)
```

`title-slide` は表示する情報を `self.info` から読み取ります。また、`extra` パラメーターを渡して追加情報を表示することもできます。

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

ヘッダーとフッターを備えたデフォルトのスライドです。`title` のデフォルトは `auto` で、現在のスライドレベルの見出しを表示することを意味します。`align` のデフォルトは `auto` で、テーマの `align` パラメーター（デフォルトは `horizon`）に従うことを意味します。フッターはテーマレベルで `footer` / `footer-right` パラメーターを通してのみ設定できます。`#slide` 自体には `footer` パラメーターはありません。

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

アウトラインスライドを表示します。`level` のデフォルトは `auto` であり、`config-common` で設定されたスライドレベルを使用します。`..args` は [`components.custom-progressive-outline`](https://touying-typ.github.io/docs/reference/components/custom-progressive-outline) に転送され、`indent`、`vspace`、`numbered`、`numbering` にはあらかじめMetropolisのスタイルに合ったデフォルト値が設定されています。

アウトラインスライドでは `place(hide(heading(..)))` を使って不可視の見出しを配置するため、スライドのヘッダーと発表者ノートのパネルの両方が `utils.display-current-heading` を通してタイトルを取得できます。

---

```typst
#focus-slide[
  ...
]
```

注意を引くために使用します。背景色は `self.colors.neutral-dark` に設定されています（Metropolisは `primary-dark` を定義していません）。

---

```typst
#new-section-slide(config: (:), level: 1, numbered: true, body)
```

指定されたタイトルで新しいセクションを作成します。すでに `config-common(new-section-slide-fn: ..)` で登録されているため、通常は `= Title` と記述することで呼び出され、手動で呼び出す必要はありません。

## 発表者ノート

Metropolisは独自の `notes` 関数を定義し、`config-common(notes-fn: notes)` で登録しています。そのため、セカンドスクリーンとノート専用ビューのノートパネルにもテーマの配色が適用されます。この関数は `touying-notes` を基に構築されており、独自の実装で上書きできます。

```typst
#show: metropolis-theme.with(
  config-common(notes-fn: my-notes),
)
```

詳細は[発表者ノート](../tutorials/speaker-notes.md)を参照してください。

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
