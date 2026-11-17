---
sidebar_position: 4
---

# Universityテーマ

この美しいテーマは [Pol Dellaiera](https://github.com/drupol) によって作成されました。

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

- `aspect-ratio`：スライドの縦横比。`"16-9"` または `"4-3"` を指定でき、既定値は `"16-9"` です。
- `align`：スライド内の配置。既定値は `top` です。
- `progress-bar`：スライド上部に進捗バーを表示するかどうか。既定値は `true` です。
- `header`：ヘッダーの内容。既定値は `utils.display-current-heading(level: 2, style: auto)` です。`self => self.info.title` のような関数も指定できます。
- `header-right`：ヘッダー右側の内容。既定値は `self => box(utils.display-current-heading(level: 1)) + h(.3em) + self.info.logo` で、現在の第1レベルの見出しに続けてロゴを表示します。
- `footer-columns`：フッターの3列の幅。既定値は `(25%, 1fr, 25%)` です。
- `footer-a`：1列目。既定値は `self => self.info.author` です。
- `footer-b`：2列目。既定では短いタイトルがあればそれを、なければ通常のタイトルを表示します（`self => if self.info.short-title == auto { self.info.title } else { self.info.short-title }`）。
- `footer-c`：3列目。既定値は次のとおりです。

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

Universityテーマの既定の配色は次のとおりです。

```typc
config-colors(
  primary: rgb("#04364A"),
  secondary: rgb("#176B87"),
  tertiary: rgb("#448C95"),
  neutral-lightest: rgb("#ffffff"),
  neutral-darkest: rgb("#000000"),
)
```

`config-colors()` で配色を変更できます。

## スライド関数群

Universityテーマは次の独自スライド関数を提供します。

```typst
#title-slide(config: (:), extra: none, ..args)
```

`title-slide` は `self.info` から表示情報を取得します。`extra` で追加の情報を指定できます。`..args` の名前付き引数は `self.info` の同名フィールドを上書きするため、`logo` や配列形式の `authors` を直接渡すこともできます。

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

ヘッダーとフッターを備える通常のスライド関数です。`#slide` 自体には `title` パラメーターがなく、テーマの `header` によりヘッダーが決まります。既定では現在の第2レベルの見出しを表示します。`align: auto` はテーマ側の配置（既定値は `top`）に従います。

---

```typst
#new-section-slide(config: (:), level: 1, numbered: true, body)
```

指定した見出しで新しいセクションを開始します。`config-common(new-section-slide-fn: ..)` に登録されているため、通常は `= Title` の見出しによって自動的に呼び出されます。

### 強調スライド

```typst
#focus-slide(background-img: ..., background-color: ...)[
  ...
]
```

聴衆の注意を引くスライドです。`background-color` と `background-img` の既定値はともに `none` で、その場合は `self.colors.primary` が背景色になります。

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

Universityは独自の `notes` 関数を `config-common(notes-fn: notes)` に登録します。セカンドスクリーンとノート専用表示にもテーマの配色が適用されます。`touying-notes` を基にしており、独自実装で上書きできます。

```typst
#show: university-theme.with(
  config-common(notes-fn: my-notes),
)
```

詳しくは[発表者ノート](../tutorials/speaker-notes.md)を参照してください。

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
