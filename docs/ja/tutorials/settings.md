---
sidebar_position: 3
---

# 設定と構成

## グローバルスタイル

Touyingでいうグローバルスタイルとは、`#set text(size: 20pt)` など、あらゆる場所に適用する必要があるsetルールやshowルールを指します。

Touyingのテーマは独自のグローバルスタイルの一部をカプセル化して、`#self.methods.init` に配置しています。例えば、simpleテーマは次の内容をカプセル化しています。

```typst
config-methods(
  init: (self: none, body) => {
    set text(size: 25pt)
    show footnote.entry: set text(size: .6em)
    show heading.where(level: 1): set text(1.4em)

    body
  },
)
```

テーマの作成者ではなく、単に独自のグローバルスタイルをスライドにいくつか追加したい場合は、`#show: xxx-theme.with()` の前または後に簡単に配置できます。例えば、metropolisテーマでは、次のグローバルスタイルを自分で追加することを推奨しています。

```typst
#set text(font: "Fira Sans", weight: "light", size: 20pt)
#show math.equation: set text(font: "Fira Math")
#set strong(delta: 100)
#set par(justify: true)
```

## グローバル情報

Beamerと同様に、Touyingは統一されたAPI設計を通じてグローバル情報をより適切に管理できるようにし、異なるテーマ間を簡単に切り替えられるようにします。グローバル情報はその典型的な例です。

スライドのタイトル、サブタイトル、著者、日付、所属、連絡先、ロゴの情報は次のように設定できます。

```typc
config-info(
  title: [Title],
  subtitle: [Subtitle],
  author: [Authors],
  date: datetime.today(),
  institution: [Institution],
  contact: [contact\@mail.com],
  logo: [logo.png],
  extra: (supervisor:[Supervisor],),
)
```

さらに、ほかの属性ではカバーされないプレゼンテーション情報を維持するため、追加情報を渡すこともできます。

後から `self.info` を通してそれらにアクセスできます。

この情報は一般にテーマの `title-slide`、`header`、`footer` で使用されます。例えば `#show: metropolis-theme.with(aspect-ratio: "16-9", footer: self => self.info.institution)` です。

`date` は `datetime` 形式と `content` 形式を受け付けます。`datetime` 形式の日付の表示形式は次のように変更できます。

```typc
config-common(datetime-format: "[year]-[month]-[day]")
```

## 設定関数の概要

Touyingのすべての設定は一連の `config-*` 関数を通してテーマへ渡され、それらが返す辞書は `self` にマージされます。

| 関数 | 用途 |
|------|------|
| `config-common(..)` | 共通設定：`slide-fn`、`slide-level`、`handout`、`preamble`、`frozen-counters` など。 |
| `config-page(..)` | ページ設定：`paper`、`margin`、`header`、`footer`、`fill` など、`set page(..)` のパラメーターに対応します。 |
| `config-info(..)` | プレゼンテーションのメタデータ。上で説明しました。 |
| `config-colors(..)` | テーマのカラーパレット。`self.colors` からアクセスします。 |
| `config-methods(..)` | `init` や `alert` などのテーマメソッド。`self.methods` からアクセスします。 |
| `config-store(..)` | テーマのカスタムストレージフィールド。`self.store` からアクセスします。 |
| `config-article(..)` | 記事モードのレイアウトオプション。後述します。 |

### 発表者ノートのパネル

`config-common(notes-fn: ..)` は発表者ノートのパネルの描画方法を決定し、セカンドスクリーンへの出力と `show-only-notes` モードの両方で使用されます。`(self: none, note: none, slide-preview: none)` を受け取り、コンテンツを返します。デフォルトは `touying-notes` です。テーマは `slide-fn` を上書きするのと同じ方法で上書きします。詳細は[発表者ノート](./speaker-notes.md)を参照してください。

### 出力モードと記事モード

| キー | デフォルト | 説明 |
|----|--------|------|
| `config-common(export-mode: ..)` | `"slides"` | 出力モード：`"slides"`、`"presentation"`、`"handout"`、`"article"` のいずれかです。`--input export-mode=article` を使ってコマンドラインから設定することもできます。 |
| `config-common(article-mode: ..)` | `false` | 記事モードに直接切り替えます。代わりに `export-mode` を優先してください。 |
| `config-common(article-theme: ..)` | `auto` | 記事モードで使用するテーマです。`auto` はTouying独自の `themes.article` を意味します。 |

`config-article(..)` は、画像を横にフロートさせる、タイトルブロックを選ぶ、設定フィールドを記事テーマへそのまま渡すなど、記事モードで使用するレイアウトを設定します。

```typst
#import "@preview/touying:0.8.0": *
#import themes.simple: *
#import themes.article: article-theme

#show: simple-theme.with(
  config-info(title: [Title], author: [Author], date: datetime.today()),
  config-common(
    export-mode: "article",
    article-theme: article-theme.with(numbering: "1.1"),
  ),
  config-article(
    wrap: (image: true),
    title-block-fn: auto,
    available-fields: (title: "info.title"),
  ),
)
```

本文では `#article-only[..]`、`#article-text[..]`、`#slides-only[..]`、`#presentation-only[..]` によって、出力ターゲットに応じて内容を含めたり除外したりします。全体像については[記事モード](../integration/article-mode)を参照してください。

## 前処理

`config-common(preamble: ...)` オプションは、プレゼンテーションの最初のスライドより前にセットアップコードを1回実行します。これは `codly` のようなパッケージと連携する場合に便利です。

```typst
#show: simple-theme.with(
  config-common(preamble: {
    codly(languages: codly-languages)
  }),
)
```

各スライドの前に処理を実行するには `slide-preamble` を、各サブスライドでコードを実行したりコンテンツを挿入したりするには `subslide-preamble` を使用します。これらはすべて、それぞれのコンテンツのページのヘッダーがレイアウトされた後、ページの主要な内容より前に実行されます。
`page-preamble` を指定することもできます。これはヘッダーより前に、はみ出したスライドなども含めて各ページで実行されます。

## showルールによる設定の上書き

`#show: touying-set-config.with(...)` を使用すると、現在のスライドとそれ以降のすべてのスライドについて任意の設定を上書きできます。これは通常の `show`/`set` ルールを記述するのと同じです。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(aspect-ratio: "16-9")

== Normal Slide

This slide uses the default settings.



== Blue Background Slide
#show: touying-set-config.with(config-page(fill: blue.lighten(80%)))
This slide has a blue background applied via `touying-set-config`.

== Red Accent Slide
#show: touying-set-config.with(config-colors(primary: red))
This slide uses a red primary color, e.g. in `#alert` boxes.

#alert[This is an alert box with red accent color.]

== Changed Cover
#show: touying-set-config.with(config-methods(
  cover: utils.alpha-changing-cover,
))
Initial Content.

#pause

Content that is hidden by showing with low alpha value.
```

## ローカル設定の上書き
特定の1枚のスライドのみに影響させたい場合は、`#slide(config: ...)[...]` を通して設定をローカルに指定できます。
```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *

>>> #show: simple-theme.with(aspect-ratio: "16-9")
== Local Config 
#slide(config:config-page(fill: purple.lighten(90%)))[
Only this slide has a light purple background, but the next slide goes back being light blue.
]
```

## 遅延適用する設定showルール
設定の変更を次のスライドの開始時まで遅延させることもできます。
`show: appendix` もこの方法で機能しますが、スライドの内容だけでなくほかの部分にも影響を与える独自の前処理などを設定するのにも便利です（config-commonは効果がないことに注意してください。設定辞書をそのまま記述することもできます）。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #import "@preview/codly:1.3.0": *
>>> #import "@preview/codly-languages:0.1.10": *

>>> #show: codly-init.with()
>>> #show: simple-theme.with(aspect-ratio: "16-9")
== Content Slide
Some content.
#show: touying-set-config.with(defer:true, config-common(appendix:true))
// 単に `show: appendix` と記述することもできる
== Appendix
Page counter does no longer increase.
#show: touying-set-config.with(defer:true, (slide-preamble:{codly(languages: codly-languages)}))
== Deferred Config Change
Now we have codly available.
```

## カウンターの固定

アニメーションを使用する場合、デフォルトでは1枚のスライド内の図や定理のカウンターはサブスライドごとに進み続けます。カウンターを固定して（サブスライド間で変化しないようにして）使用するには、次のようにします。

```typst
config-common(frozen-counters: (counter(figure.where(kind: image)),))
```

独自の図の種類に対しては、`counter(figure.where(kind: "Name"))` を渡し、セレクター自体は渡さないでください。`frozen-counters` はカウンターを期待します。これは [Theorion](../integration/theorion.md) パッケージと併用する場合にも便利です。

```typst
config-common(frozen-counters: (theorem-counter,))
```

`frozen-counters` と `config-common(freeze-slide-counter: true)` を混同しないでください。後者はスライドカウンター自体を固定し、スライドがスライド番号を消費しないようにします。[スライドカウンターと進捗](./progress/counters.md)を参照してください。

## 設定情報の取得

`touying-get-config` を使用して、スライドに保存された設定にアクセスできます。これはグローバル設定と、そのスライドに対して行った上書き設定を組み合わせたものです。

これは `context` の時点で評価され、要求した位置で文書フローに挿入されるため、コンテンツとしてのみ利用可能であることに注意してください。

### 設定全体を取得する

`touying-get-config()` を引数なしで呼び出し、設定辞書全体を取得します。その後、通常の辞書構文でネストされた値にアクセスできます。

```typst
#touying-get-config().info.author

#touying-get-config().common.handout
```

`common` のフィールドはトップレベルに登録されているため、直接アクセスできます。

```typst
#touying-get-config().handout  // .common.handout と同じ
```

### キーによる取得

ドット区切りの文字列キーを渡すと、特定の部分設定や値を直接取得できます。

```typst
#touying-get-config("info.author")

#touying-get-config("info")  // infoの部分辞書全体を返す
```

### デフォルト値

キーが存在しない場合、デフォルトでは `touying-get-config` はパニックします。代わりにフォールバック値を指定するには、`default` パラメーターを使用します。

```typst
#touying-get-config("random.dict.value", default: "default value")
```

### 独自設定へのアクセス

`touying-set-config` を通してカスタムキーを設定した場合、`show` ルールの直後から使用可能になります。

```typst
#show: touying-set-config.with((random: (dict: (value: 123))))

#touying-get-config("random.dict.value")  // "123" を表示する
```

:::warning[注意]

独自設定にアクセスする場合は、文字列キーの形式（`touying-get-config("random.dict.value")`）を使用しなければなりません。辞書アクセスを連鎖させる形式（`touying-get-config("random.dict").value`）は使用できません。後者はコンテンツ要素の `.value` にアクセスしようとするため、失敗します。

:::
