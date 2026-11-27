---
sidebar_position: 9
---

# 発表者ノート

発表者ノートは聴衆ではなく発表者自身のための内容です。属するスライドのそばに直接記述し、Touyingはその内容をスライド自体から除外します。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *
#show: simple-theme

== Photosynthesis

#align(horizon+center, text(size: 2em)[Light in, sugar out.])

#speaker-note[
  - Recap respiration from the previous section.
  - Time check: we should be at the 10 minute mark.
]
```

ノートはスライド上に一切痕跡を残しません。また、スライドごとに複数の `speaker-note[]` を記述できます。それらは自動的に1枚のノートスライドにまとめられます。

発表者ノートは主に発表ツールに情報を渡すために存在します。Touyingが対応する2つのツールは
[pdfpc](../external/pdfpc.md) と [pympress](../external/pympress.md) です。
以下のどの仕組みが必要になるかは、使用する発表ツールによって異なります。

## ノートの出力先

ノートには3つの出力先があり、それぞれ独立しています。同じ文書から1つ、2つ、あるいは3つすべてを使用できます。

| 出力先 | 有効化する方法 | 読み取るもの |
| --- | --- | --- |
| pdfpcファイル | デフォルトで有効（`enable-pdfpc: true`） | [pdfpc](../external/pdfpc.md)、付随する `.pdfpc` ファイルを介して |
| セカンドスクリーン | `config-common(show-notes-on-second-screen: ..)` | [pympress](../external/pympress.md) と任意のデュアルスクリーン対応ビューアー |
| 発表者ビュー | `config-common(show-only-notes: true)` | 発表者自身、または2つのPDFを同期できるツール |

1つ目はメタデータで、PDF上では不可視であり、`.pdfpc` ファイルとして出力できます。ほかの2つはPDFが
*含む内容*を変更するため、視覚的な出力そのものに関する選択肢です。

## セカンドスクリーン

`show-notes-on-second-screen` はページを片側（例えば右側）へ2倍に拡張し、スライドごとに集めたノートを新たに確保された半分の領域に配置します。スライド自体の寸法は維持されるため、左半分は通常のスライドのままです。その半分を全画面で投影し、もう半分を発表者自身が読みます。

```typst
#show: simple-theme.with(
  aspect-ratio: "16-9",
  config-common(show-notes-on-second-screen: right),
)
```

`top`、`bottom`、`left`、`right` のすべてが受け付けられます。デフォルトは `none` で、どの側にもノートを表示しません。

これは [pympress](../external/pympress.md) が想定するレイアウトです。付随ファイルを使わずに、
スライド側の半分をプロジェクターに、ノート側の半分を発表者の画面に表示します。
リンク先のページには具体的な例と結果のスクリーンショットがあります。

`config-page(background: ..)` はスライドに属し、スライド自体の半分の領域のみを覆います。ノート側の半分は別途スタイルを設定します。[パネルの装飾](#パネルの装飾)を参照してください。

## 発表者ビュー

`show-only-notes: true` はページ上の内容を入れ替えます。ノートがページの主要な内容となり、スライド全体が縮小され、隅にプレビューとして配置されます。

```typst
#show: simple-theme.with(
  aspect-ratio: "16-9",
  config-common(show-only-notes: true),
)
```

この機能には誤解しやすい点が2つあります。

**何も削除しません。** サブスライドを含め、ページ数は通常のコンパイルとまったく同じです。すべてのページが残り、スライドの代わりにノートを表示するだけです。したがって発表者向け出力の7ページ目とスライドの7ページ目が対応し、2つを並べて利用できます。

**これは2つ目の出力であり、そこから発表するモードではありません。** 1つのソースから2つのPDFを生成します。プロジェクター用の通常のPDFと、フラグを設定した発表者向けのPDFです。組み込みのコマンドラインスイッチはないため、コンパイルの間にファイルを編集したくない場合は、自分で切り替えられるようにしてください。

```typst
#let notes-export = sys.inputs.at("notes", default: none) != none

#show: simple-theme.with(
  aspect-ratio: "16-9",
  config-common(show-only-notes: notes-export),
)
```

```sh
typst compile slides.typ slides.pdf
typst compile slides.typ --input notes=true notes.pdf
```

## ノートを特定のサブスライドに限定する

デフォルトでは、ノートは通常の内容とまったく同じように、記述された一時停止位置に従います。`#pause` の後にあるノートは、そのサブスライド以降でのみ表示されます。`subslide:` でこの動作を上書きできます。

```typst
// サブスライド2でのみ
#speaker-note(subslide: 2)[Mention the caveat here.]

// 配置されている位置に関係なく、すべてのサブスライドで
#speaker-note(subslide: none)[Keep an eye on the clock.]
```

ノートの本文自体に `#pause` を含めて、サブスライドをまたいでノートの一部を段階的に表示することもできます。

## pdfpc向けのMarkdownノート

pdfpcはノートをMarkdownとして描画します。`mode` はTouyingがノートをpdfpcファイルにシリアライズする方法を制御します。どちらの場合でも、通常のTypstを記述します。

```typst
#speaker-note(mode: "md")[
  - a bullet
  - *bold*
]
```

`mode: "typ"`（デフォルト）では、その `*bold*` は `*bold*` として書き出されます。`mode: "md"` ではpdfpcが想定する `**bold**` に変換されます。見出し、リンク、強調も同様に変換されます。

## pdfpc向けの出力

`enable-pdfpc: true`（デフォルト）では、Touyingはすべてのノートをスライド構造とともに
文書のpdfpcメタデータに記録します。PDFの隣に `.pdfpc` ファイルとして書き出すことで、
pdfpcにノート、オーバーレイ構造、タイミングを提供します。

ファイルを生成する方法は2つあります。コンパイル後の `typst eval`、または
バンドル出力中の `#pdfpc.bundle-assets()` であり、後者では2回目のコマンドは不要です。どちらも
[pdfpc](../external/pdfpc.md) のページで、発表時間、カウントダウン、スライドの切り替えを設定する `#pdfpc.config(..)` とともに
説明されています。

そのページでは、Touyingが保持している低レベルの呼び出し `#pdfpc.speaker-note(..)` についても
説明しています。これは [Polylux](https://polylux.dev/book/external/pdfpc.html) との互換性のためのものです。
生の文字列をメタデータに直接書き込み、以下の引数は一切受け取らないため、
既存のスライド一式を移植する場合を除いて `#speaker-note[..]` を優先してください。どちらも最終的には同じ場所に記録されます。
1つのスライド上の複数のノートは、そのスライドの項目に連結されます。

## パネルの装飾

ノートを表示するパネル（セカンドスクリーンまたはノート専用ビュー）は、テーマによって描画されます。これは `notes` 関数を `config-common(notes-fn: ..)` に指定することで行われます。同梱されているすべてのテーマがこの関数を設定しているため、ノートはテーマのほかの部分と統一された見た目になります。

テーマがない場合のデフォルトは `touying-notes` です。これはレイアウトを処理し、スタイルの設定は利用者に任せます。`header`、`header-fill`、`fill`、`note-setting`、`preview-setting` を使用します。独自のテーマ向けにこの関数を記述する方法は、[独自テーマを作成する](build-your-own-theme.md)で説明しています。
