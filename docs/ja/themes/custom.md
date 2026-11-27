---
sidebar_position: 7
---

# 独自テーマ

組み込みテーマのどれもがニーズに十分合わない場合は、2つの選択肢があります。

1. **既存のテーマを拡張する** — テーマファイルをローカルにコピーし、変更します。
2. **新しいテーマを一から作る** — 独自の `xxx-theme` 関数を実装します。

どちらの方法も[独自テーマを作成する](../tutorials/build-your-own-theme.md)チュートリアルで詳しく説明しています。

## 簡単な変更

既存のテーマに小さな調整を加える場合は、別途テーマファイルを作成する必要はありません。個々の設定をその場で上書きできます。

```example
#import "@preview/touying:0.8.0": *
#import themes.metropolis: *

#show: metropolis-theme.with(
  aspect-ratio: "16-9",
  // プライマリカラーを上書きする
  config-colors(primary: rgb("#1a6b8a")),
  // フッターの内容を変更する
  footer: self => self.info.author,
  config-info(
    title: [My Presentation],
    author: [Author Name],
    date: datetime.today(),
  ),
)

#title-slide()

= Section

== Slide

Content with the custom color.
```

## テーマをローカルにコピーする

より根本的な構造変更を加えるには、テーマのソースファイルをプロジェクトにコピーします。

1. Touyingリポジトリの `themes/` から該当するファイル（例えば `themes/metropolis.typ`）をダウンロードします。
2. 先頭のインポートを `#import "../src/exports.typ": *` から `#import "@preview/touying:0.8.0": *` に変更します。
3. 組み込みテーマの代わりにローカルのコピーをインポートします。

```typst
#import "@preview/touying:0.8.0": *
#import "metropolis.typ": *   // ローカルのコピー

#show: metropolis-theme.with(
  aspect-ratio: "16-9",
  config-info(title: [Title]),
)
```

これで、他のプロジェクトに影響を与えることなく `metropolis.typ` を自由に編集できます。


## 発表者ノートのパネルをカスタマイズする

テーマはスライドの見た目だけでなく、[発表者ノート](../tutorials/speaker-notes.md)のパネルの見た目も決定します。セカンドスクリーンとノート専用ビューは、どちらも `config-common(notes-fn: ..)` に渡された関数によって描画されます。Touying 0.8.0以降、同梱されているすべてのテーマが独自の `notes` 関数を記述して登録するため、ノートパネルの見た目はテーマのほかの部分と一致します。

独自の関数を記述する場合は、`touying-notes` を基に構築してください。この関数はレイアウトを処理し、スタイルの設定を利用者に任せます。

- `header`：パネル上部の帯です。内容に合わせて大きさが決まり、`none` を渡すと完全に取り除かれます。
- `header-fill`：帯の背景の塗りつぶしです。色だけでなく `rect` や `image` も指定できます。
- `fill`：パネルの残りの部分の背景の塗りつぶしです。
- `note-setting`：パネルの主要な内容であるノート本文に適用される設定関数です。
- `preview-setting`：`show-only-notes` モードで表示される縮小されたスライドのプレビューに適用される設定関数です。

ノートパネルは `config-page(..)` を使用しません。背景は `fill` と `header-fill` に直接渡してください。

より詳しい手順については、[独自テーマを作成する・発表者ノートのカスタマイズ](../tutorials/build-your-own-theme.md#発表者ノートのカスタマイズ)を参照してください。

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
  config-info(title: [My Presentation]),
)

== Slide

Content.

#speaker-note[Remember to slow down here.]
```

## 特殊なスライドのタイトルを取得可能にする

スライドのヘッダーとノートパネルの両方のタイトルは、`utils.display-current-heading` を通して現在の見出しから読み取られます。タイトルスライド、アウトラインスライド、終了スライドなどの特殊なスライドには実際の見出しがないことが多いため、タイトルがそのまま「消えて」しまいます。

同梱されているテーマでは、このようなスライドに非表示の見出しを配置します。

```typst
place(hide(heading(
  level: self.slide-level,
  title,
  bookmarked: false,
  outlined: false,
  numbering: none,
)))
```

`hide` によって見出しが不可視になり、`place` によってレイアウト上の領域を占有しなくなります。また、`outlined: false` と `bookmarked: false` を組み合わせると、アウトラインとPDFブックマークに含まれなくなります。ただし、`utils.display-current-heading` では引き続き見出しを取得できます。aqua、dewdrop、metropolis、stargazerの `outline-slide` とstargazerの `ending-slide` は、いずれもこのパターンを使用しています。

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

テーマ内のヘルパーコンポーネント（例えばstargazerの `tblock`）では、`self.colors` を読み取る必要があることがよくあります。この用途には `touying-fn-wrapper-raw` を優先して使用してください。`#alert` と同様にその場で展開され、`#pause` / `#uncover` のアニメーション構造を分断しないため、ラップされたコンポーネントは通常どおりアニメーションに参加できます。

コールバックの最初のパラメーターは `(self: none) => ..` と記述する必要があります。

```typst
#let _tblock(self: none, title: none, it) = { /* .. */ }

#let tblock(title: none, it) = touying-fn-wrapper-raw(
  _tblock.with(title: title, it),
)
```
