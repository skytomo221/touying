---
sidebar_position: 4
---

# Universityテーマ

この美しいテーマは [Pol Dellaiera](https://github.com/drupol) の提供によるものです。

## 初期化

次のコードでテーマを初期化できます。

```typst
#import "@preview/touying:0.8.0": *
#import themes.university: *

#import "@preview/numbly:0.1.0": numbly

#show: university-theme.with(
  aspect-ratio: "16-9",
  config-info(
    title: [Title],
    subtitle: [Subtitle],
    author: [Authors],
    date: datetime.today(),
    institution: [Institution],
    contact: [contact\@mail.com],
    logo: emoji.school,
  ),
)

#set heading(numbering: numbly("{1}.", default: "1.1"))

#title-slide()
```

`university-theme` 関数は次のパラメーターを受け取ります。

- `aspect-ratio`：スライドのアスペクト比です。"16-9" または "4-3" のいずれかで、デフォルトは "16-9" です。
- `align`：スライドの配置です。デフォルトは `top` です。
- `progress-bar`：スライドの上部に進捗バーを表示するかどうかです。デフォルトは `true` です。
- `header`：ヘッダーに表示される内容です。デフォルトは `utils.display-current-heading(level: 2, style: auto)` です。または `self => self.info.title` のような関数を渡すこともできます。
- `header-right`：ヘッダーの右側に表示される内容です。デフォルトは `self => box(utils.display-current-heading(level: 1)) + h(.3em) + self.info.logo` で、つまり現在の第1レベルの見出しの後にロゴを表示します。
- `footer-columns`：フッターの3列の幅です。デフォルトは `(25%, 1fr, 25%)` です。
- `footer-a`：第1列です。デフォルトは `self => self.info.author` です。
- `footer-b`：第2列です。デフォルトは `self => if self.info.short-title == auto { self.info.title } else { self.info.short-title }` です。
- `footer-c`：第3列です。デフォルトは次のとおりです。

```typst
self => {
  h(1fr)
  utils.display-info-date(self)
  h(1fr)
  context utils.slide-counter.display() + " / " + utils.last-slide-number
  h(1fr)
}
```

## 配色

Universityテーマではデフォルトで次の配色を使用します。

```typc
config-colors(
  primary: rgb("#04364A"),
  secondary: rgb("#176B87"),
  tertiary: rgb("#448C95"),
  neutral-lightest: rgb("#ffffff"),
  neutral-darkest: rgb("#000000"),
)
```

この配色は `config-colors()` を使用して変更できます。

## スライド関数群

Universityテーマは一連のカスタムスライド関数を提供します。

```typst
#title-slide(config: (:), extra: none, ..args)
```

`title-slide` 関数は表示する情報を `self.info` から読み取ります。また、`extra` パラメーターを渡して追加情報を表示することもできます。`..args` の名前付き引数は `self.info` 内の同名のフィールドを上書きするため、`logo` や配列型の `authors` を直接渡すこともできます。

---

```typst
#slide(
  config: (:),
  repeat: auto,
  setting: body => body,
  composer: auto,
  align: auto,
)[
  ...
]
```

ヘッダーとフッターを備えた標準のスライド関数です。`#slide` 自体には `title` パラメーターがありません。ヘッダーはテーマの `header` パラメーターで決まり、デフォルトでは現在の第2レベルの見出しを表示します。`align` のデフォルトは `auto` で、テーマの `align` パラメーター（デフォルトは `top`）に従うことを意味します。

---

```typst
#new-section-slide(config: (:), level: 1, numbered: true, body)
```

指定されたタイトルで新しいセクションを開始します。すでに `config-common(new-section-slide-fn: ..)` で登録されているため、通常は `= Title` と記述することで呼び出され、手動で呼び出す必要はありません。

### 強調スライド

```typst
#focus-slide(background-img: ..., background-color: ...)[
  ...
]
```

聴衆の注意を引くために使用します。`background-color` と `background-img` はどちらもデフォルトが `none` であり、その場合の背景色は `self.colors.primary` です。

### 行列スライド

```typst
#matrix-slide(columns: ..., rows: ...)[
  ...
][
  ...
]
```

[ドキュメント](https://polylux.dev/book/themes/gallery/university.html)を参照してください。

## 発表者ノート

Universityは独自の `notes` 関数を定義し、`config-common(notes-fn: notes)` で登録しています。そのため、セカンドスクリーンとノート専用ビューのノートパネルにもテーマの配色が適用されます。この関数は `touying-notes` を基に構築されており、独自の実装で上書きできます。

```typst
#show: university-theme.with(
  config-common(notes-fn: my-notes),
)
```

詳細は[発表者ノート](../tutorials/speaker-notes.md)を参照してください。

## 使用例

```example
#import "@preview/touying:0.8.0": *
#import themes.university: *

#import "@preview/numbly:0.1.0": numbly

#show: university-theme.with(
  aspect-ratio: "16-9",
  config-info(
    title: [Title],
    subtitle: [Subtitle],
    author: [Authors],
    date: datetime.today(),
    institution: [Institution],
    contact: [contact\@mail.com],
    logo: emoji.school,
  ),
)

#set heading(numbering: numbly("{1}.", default: "1.1"))

#title-slide(authors: ([Author A], [Author B]))

= The Section

== Slide Title

#lorem(40)

#focus-slide[
  Another variant with primary color in background...
]

#matrix-slide[
  left
][
  middle
][
  right
]

#matrix-slide(columns: 1)[
  top
][
  bottom
]

#matrix-slide(columns: (1fr, 2fr, 1fr), ..(lorem(8),) * 9)
```
