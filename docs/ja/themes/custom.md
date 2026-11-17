---
sidebar_position: 7
---

# 独自テーマ

組み込みテーマが目的に合わない場合には、次の2つの方法があります。

1. **既存のテーマを拡張する** — テーマファイルをローカルにコピーして変更します。
2. **新しいテーマを一から作る** — 独自の `xxx-theme` 関数を実装します。

どちらも[独自テーマを作成する](../tutorials/build-your-own-theme.md)チュートリアルで詳しく説明します。

## 簡単な変更

既存のテーマを少し変更するだけなら、新たなテーマファイルは不要です。個別の設定をその場で上書きできます。

```example
#import "@preview/touying:0.8.0": *
#import themes.metropolis: *

#show: metropolis-theme.with(
  aspect-ratio: "16-9",
  // 基本色を上書きする
  config-colors(primary: rgb("#1a6b8a")),
  // フッターの内容を変更する
  footer: self => self.info.author,
  config-info(
    title: [私のプレゼンテーション],
    author: [著者名],
    date: datetime.today(),
  ),
)

#title-slide()

= Section

== Slide

独自の配色を使った内容です。
```

## テーマをローカルにコピーする

構造を大きく変更する場合は、テーマのソースファイルをプロジェクトにコピーします。

1. Touyingリポジトリの `themes/` から対象のファイル（例：`themes/metropolis.typ`）を取得します。
2. ファイル先頭のインポートを `#import "../src/exports.typ": *` から `#import "@preview/touying:0.8.0": *` に変更します。
3. 組み込みテーマの代わりにローカルのコピーをインポートします。

```typst
#import "@preview/touying:0.8.0": *
#import "metropolis.typ": *   // ローカルにコピーしたファイル

#show: metropolis-theme.with(
  aspect-ratio: "16-9",
  config-info(title: [タイトル]),
)
```

これで他のプロジェクトに影響を与えずに `metropolis.typ` を編集できます。


## 発表者ノートのパネルをカスタマイズする

テーマはスライドだけでなく[発表者ノート](../tutorials/speaker-notes.md)のパネルの見た目も決定します。セカンドスクリーンとノート専用表示は、いずれも `config-common(notes-fn: ..)` に設定した関数で描画されます。Touying 0.8.0以降、組み込みテーマはそれぞれ専用の `notes` 関数を登録し、全体の見た目を統一しています。

自分で実装する際には、レイアウトを担当する `touying-notes` を基にして装飾を指定します。

- `header`：パネル上部の帯。内容に応じて高さが変わり、`none` で非表示になります。
- `header-fill`：帯の背景。色のほか `rect` や `image` も指定できます。
- `fill`：パネルの残りの領域の背景。
- `note-setting`：パネルの主要部分であるノート本文に適用する設定関数。
- `preview-setting`：`show-only-notes` モードに表示される縮小スライドのプレビューに適用する設定関数。

ノート用パネルでは `config-page(..)` を使用しません。背景は `fill` と `header-fill` に直接指定してください。

詳しくは[独自テーマを作成する・ノートのカスタマイズ](../tutorials/build-your-own-theme.md#発表者ノートのカスタマイズ)を参照してください。

```example
#import "@preview/touying:0.8.0": *
#import themes.metropolis: *

#let my-notes(self: none, ..args) = touying-notes(
  self: self,
  header: self => pad(1em, text(
    fill: self.colors.neutral-lightest,
    utils.display-current-heading(depth: self.slide-level),
  )),
  header-fill: self.colors.primary,
  fill: self.colors.neutral-lightest,
  note-setting: note => pad(1.5em, text(size: .8em, note)),
  ..args,
)

#show: metropolis-theme.with(
  aspect-ratio: "16-9",
  config-common(notes-fn: my-notes, show-notes-on-second-screen: right),
  config-info(title: [私のプレゼンテーション]),
)

== Slide

Content.

#speaker-note[ここではゆっくり話すこと。]
```

## 特殊なスライドのタイトルを取得可能にする

スライドのヘッダーとノートパネルのタイトルは、`utils.display-current-heading` を通じて現在の見出しから取得されます。タイトル、アウトライン、終了スライドなどの特殊なスライドには見出しがないことが多く、そのままではタイトルが表示されません。

組み込みテーマでは、こうしたスライドに不可視の見出しを配置します。

```typst
place(hide(heading(
  level: self.slide-level,
  title,
  bookmarked: false,
  outlined: false,
  numbering: none,
)))
```

`hide` は見出しを不可視にし、`place` はレイアウト上の領域を消費しないようにします。`outlined: false` と `bookmarked: false` によりアウトラインやPDFのブックマークには現れませんが、`utils.display-current-heading` では取得できます。aqua、dewdrop、metropolis、stargazerの `outline-slide` とstargazerの `ending-slide` はこの方式を使います。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#let thanks-slide(config: (:), title: [Thanks!]) = touying-slide-wrapper(self => {
  let body = {
    place(hide(heading(
      level: self.slide-level,
      title,
      bookmarked: false,
      outlined: false,
      numbering: none,
    )))
    align(center + horizon, text(2em, weight: "bold", title))
  }
  touying-slide(self: self, config: config, body)
})

#show: simple-theme.with(aspect-ratio: "16-9")

== A Slide

Content.

#thanks-slide()
```

## `self` を参照するヘルパー関数

テーマ内のヘルパーコンポーネント（stargazerの `tblock` など）は、しばしば `self.colors` を参照します。この場合は `touying-fn-wrapper-raw` が推奨されます。`#alert` と同じようにその場で展開され、`#pause` や `#uncover` のアニメーション構造を崩さずに動作します。

コールバックの最初のパラメーターは `(self: none) => ..` と記述する必要があります。

```typst
#let _tblock(self: none, title: none, it) = { /* .. */ }

#let tblock(title: none, it) = touying-fn-wrapper-raw(
  _tblock.with(title: title, it),
)
```
