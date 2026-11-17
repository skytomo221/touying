---
sidebar_position: 3
---

# 設定と構成

## グローバルスタイル

Touyingでのグローバルスタイルとは、`#set text(size: 20pt)` のように文書全体へ適用するsetルールやshowルールです。

Touyingのテーマは独自のグローバルスタイルの一部を `#self.methods.init` にまとめています。たとえばsimpleテーマでは次のように定義されています。

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

テーマを作成するのではなく独自のスタイルを少し追加したいだけなら、`#show: xxx-theme.with()` の前後に記述できます。たとえばmetropolisテーマでは次のグローバルスタイルの追加が推奨されています。

```typst
#set text(font: "Fira Sans", weight: "light", size: 20pt)
#show math.equation: set text(font: "Fira Math")
#set strong(delta: 100)
#set par(justify: true)
```

## グローバル情報

TouyingはBeamerと同様に、統一されたAPIでグローバル情報を管理し、テーマ間の切り替えを簡単にします。プレゼンテーションのメタデータがその代表例です。

スライドのタイトル、サブタイトル、著者、日付、所属、連絡先、ロゴは次のように設定できます。

```typc
config-info(
  title: [タイトル],
  subtitle: [サブタイトル],
  author: [著者],
  date: datetime.today(),
  institution: [所属],
  contact: [contact\@mail.com],
  logo: [logo.png],
  extra: (supervisor:[指導者],),
)
```

定義済みの属性にない追加情報も渡せます。

これらの情報には `self.info` からアクセスできます。

通常はテーマの `title-slide`、`header`、`footer` などで利用します。例：`#show: metropolis-theme.with(aspect-ratio: "16-9", footer: self => self.info.institution)`。

`date` には `datetime` または `content` を指定できます。`datetime` の表示形式は次のように変更できます。

```typc
config-common(datetime-format: "[year]-[month]-[day]")
```

## 設定関数の概要

Touyingの設定は `config-*` 関数群を通してテーマに渡され、それらが返す辞書は `self` に統合されます。

| 関数 | 用途 |
|------|------|
| `config-common(..)` | `slide-fn`、`slide-level`、`handout`、`preamble`、`frozen-counters` などの共通設定。 |
| `config-page(..)` | `set page(..)` に相当する `paper`、`margin`、`header`、`footer`、`fill` などのページ設定。 |
| `config-info(..)` | 前述のプレゼンテーションのメタデータ。 |
| `config-colors(..)` | `self.colors` からアクセスできるテーマのカラーパレット。 |
| `config-methods(..)` | `init` や `alert` などのテーマメソッド。`self.methods` から参照します。 |
| `config-store(..)` | `self.store` から参照する独自の保存フィールド。 |
| `config-article(..)` | 後述する記事モードのレイアウト設定。 |

### 発表者ノートのパネル

`config-common(notes-fn: ..)` は発表者ノート用パネルの描画方法を指定します。セカンドスクリーンと `show-only-notes` モードの両方で使用されます。`(self: none, note: none, slide-preview: none)` を受け取り、内容を返します。既定値は `touying-notes` です。テーマからは `slide-fn` と同じ方法で上書きできます。詳しくは[発表者ノート](./speaker-notes.md)を参照してください。

### 出力モードと記事モード

| キー | 既定値 | 説明 |
|----|--------|------|
| `config-common(export-mode: ..)` | `"slides"` | 出力モード。`"slides"`、`"presentation"`、`"handout"`、`"article"` のいずれか。`--input export-mode=article` でコマンドラインからも指定できます。 |
| `config-common(article-mode: ..)` | `false` | 記事モードへの直接切り替え。代わりに `export-mode` の使用を推奨します。 |
| `config-common(article-theme: ..)` | `auto` | 記事モードのテーマ。`auto` はTouying標準の `themes.article` を意味します。 |

`config-article(..)` で記事モードのレイアウトを設定できます。画像を横に回り込ませる、タイトルブロックを選ぶ、記事テーマへ設定を渡すといった操作が可能です。

```typst
#import "@preview/touying:0.8.0": *
#import themes.simple: *
#import themes.article: article-theme

#show: simple-theme.with(
  config-info(title: [タイトル], author: [著者], date: datetime.today()),
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

本文では `#article-only[..]`、`#article-text[..]`、`#slides-only[..]`、`#presentation-only[..]` で出力先に応じた内容の切り替えができます。詳しくは[記事モード](../integration/article-mode)を参照してください。

## 前処理

`config-common(preamble: ...)` は、プレゼンテーションの最初のスライドの前に設定コードを一度実行します。`codly` などのパッケージ連携に便利です。

```typst
#show: simple-theme.with(
  config-common(preamble: {
    codly(languages: codly-languages)
  }),
)
```

各スライドの前に処理を実行するには `slide-preamble`、各サブスライドで処理や内容の挿入を行うには `subslide-preamble` を使います。いずれもページのヘッダーがレイアウトされた後、本文が配置される前に実行されます。
`page-preamble` は、はみ出しによって生じたページも含め、各ページのヘッダーより前に実行されます。

## showルールによる設定の上書き

通常のshowルールやsetルールと同様に、`#show: touying-set-config.with(...)` で現在のスライドと後続のすべてのスライドの設定を上書きできます。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(aspect-ratio: "16-9")

== 通常のスライド

このスライドは既定の設定を使います。



== Blue 背景 Slide
#show: touying-set-config.with(config-page(fill: blue.lighten(80%)))
このスライドには `touying-set-config` による青い背景が適用されています。

== 赤い強調色のスライド
#show: touying-set-config.with(config-colors(primary: red))
このスライドでは `#alert` などの基本色が赤くなります。

#alert[This is an alert box with red accent color.]

== 変更したカバー
#show: touying-set-config.with(config-methods(
  cover: utils.alpha-changing-cover,
))
Initial 内容.

#pause

低いアルファ値で薄く表示される内容です。
```

## ローカル設定の上書き
特定のスライドだけに適用する場合は、`#slide(config: ...)[...]` でローカルに指定します。
```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *

>>> #show: simple-theme.with(aspect-ratio: "16-9")
== ローカル設定 
#slide(config:config-page(fill: purple.lighten(90%)))[
このスライドだけ背景が薄紫色になり、次のスライドでは薄青色に戻ります。
]
```

## 遅延適用する設定showルール
設定の変更を次のスライドの開始時まで遅らせることもできます。
`show: appendix` もこの仕組みで動作します。スライド本文以外にも影響する独自の前処理などの設定にも便利です（この場面では `config-common` は効果を持たないため、設定辞書を直接書いても構いません）。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #import "@preview/codly:1.3.0": *
>>> #import "@preview/codly-languages:0.1.10": *

>>> #show: codly-init.with()
>>> #show: simple-theme.with(aspect-ratio: "16-9")
== 内容 Slide
いくつかの内容です。
#show: touying-set-config.with(defer:true, config-common(appendix:true))
// `show: appendix` と書くだけでもよい
== 付録
ページカウンターはこれ以上増加しません。
#show: touying-set-config.with(defer:true, (slide-preamble:{codly(languages: codly-languages)}))
== 遅延適用する設定変更
これでcodlyが使えるようになりました。
```

## カウンターの固定

アニメーションを使うと、既定では図や定理のカウンターがサブスライドごとに進みます。サブスライド間で値を固定するには次のようにします。

```typst
config-common(frozen-counters: (counter(figure.where(kind: image)),))
```

独自の図の種類には、セレクター自体ではなく `counter(figure.where(kind: "Name"))` を渡します。`frozen-counters` はカウンターを要求します。[Theorion](../integration/theorion.md) と組み合わせる際にも役立ちます。

```typst
config-common(frozen-counters: (theorem-counter,))
```

`frozen-counters` と `config-common(freeze-slide-counter: true)` を混同しないでください。後者はスライドカウンター自体を固定し、そのスライドに番号を割り当てない設定です。詳しくは[スライドカウンターと進捗](./progress/counters.md)を参照してください。

## 設定情報の取得

`touying-get-config` でスライドに保存された設定を取得できます。グローバル設定とそのスライドでの上書き設定を統合した値が返されます。

これは `context` 時に評価され、要求した位置の文書フローに挿入されるため、内容としてのみ利用できます。

### 設定全体を取得する

`touying-get-config()` を引数なしで呼び出すと設定辞書全体を取得できます。入れ子の値には通常の辞書構文でアクセスします。

```typst
#touying-get-config().info.author

#touying-get-config().common.handout
```

`common` のフィールドはトップレベルに登録されるため、直接参照できます。

```typst
#touying-get-config().handout  // .common.handout と同じ
```

### キーによる取得

ドット区切りの文字列キーを渡すと、特定の設定や値を直接取得できます。

```typst
#touying-get-config("info.author")

#touying-get-config("info")  // infoの部分辞書全体を返す
```

### 既定値

キーが存在しない場合、`touying-get-config` は既定でパニックします。代替値を指定するには `default` パラメーターを使います。

```typst
#touying-get-config("random.dict.value", default: "default value")
```

### 独自設定へのアクセス

`touying-set-config` で独自のキーを設定すると、showルールの直後から利用できます。

```typst
#show: touying-set-config.with((random: (dict: (value: 123))))

#touying-get-config("random.dict.value")  // 「123」を表示
```

:::warning[注意]

独自設定にアクセスする場合は、辞書アクセスを連結する `touying-get-config("random.dict").value` ではなく、`touying-get-config("random.dict.value")` のように文字列キーを渡してください。前者は内容要素に対して `.value` を参照しようとして失敗します。

:::
