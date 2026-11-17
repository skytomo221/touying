---
sidebar_position: 9
---

# 発表者ノート

発表者ノートは聴衆ではなく発表者のための内容です。対応するスライドの近くに直接記述できますが、Touyingはスライド本体には表示しません。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *
#show: simple-theme

== Photosynthesis

#align(horizon+center, text(size: 2em)[Light in, sugar out.])

#speaker-note[
  - 前のセクションの呼吸に関する内容を振り返ります。
  - 時間を確認します。ここで10分経過している想定です。
]
```

ノートはスライドには表示されません。1つのスライドに複数の `speaker-note[]` を書くと、自動的に1つのノート用スライドにまとめられます。

発表者ノートは主に発表ツールへ情報を渡すためのものです。Touyingは [pdfpc](../external/pdfpc.md) と [pympress](../external/pympress.md) に対応しています。以下のどの方法を使うかは発表ツールによって異なります。

## ノートの出力先

ノートには互いに独立した3つの出力先があります。同じ文書で1つだけ、2つ、またはすべてを利用できます。

| 出力先 | 有効化する方法 | 対応ツール |
| --- | --- | --- |
| pdfpcファイル | 既定で有効（`enable-pdfpc: true`） | `.pdfpc` ファイル経由の [pdfpc](../external/pdfpc.md) |
| セカンドスクリーン | `config-common(show-notes-on-second-screen: ..)` | [pympress](../external/pympress.md) などのデュアルスクリーン対応ビューアー |
| 発表者ビュー | `config-common(show-only-notes: true)` | 発表者本人、または2つのPDFを同期できるツール |

最初の方法はPDFに見えないメタデータを `.pdfpc` ファイルとして出力します。ほかの2つはPDFの*内容自体*を変更するため、表示方法の選択になります。

## セカンドスクリーン

`show-notes-on-second-screen` はページを片側（たとえば右側）へ拡張し、増えた領域にスライドごとのノートを配置します。スライド自体の寸法は変わらないため、片側を全画面で投影し、もう片側を発表者が確認できます。

```typst
#show: simple-theme.with(
  aspect-ratio: "16-9",
  config-common(show-notes-on-second-screen: right),
)
```

`top`、`bottom`、`left`、`right` を指定できます。既定値の `none` はノートを表示しません。

これは [pympress](../external/pympress.md) が想定するレイアウトです。別ファイルなしで、スライド部分をプロジェクターに、ノート部分を発表者の画面に表示できます。実際の例とスクリーンショットはリンク先を参照してください。

`config-page(background: ..)` はスライド側にのみ適用されます。ノート側の装飾は別途指定します。詳しくは[パネルの装飾](#styling-the-panel)を参照してください。

## 発表者ビュー

`show-only-notes: true` にすると、ページの主な内容がノートになり、元のスライド全体は隅に縮小プレビューとして表示されます。

```typst
#show: simple-theme.with(
  aspect-ratio: "16-9",
  config-common(show-only-notes: true),
)
```

ここには特に注意すべき点が2つあります。

**ページは削除されません。** サブスライドを含むページ数は通常のコンパイルと同じです。表示内容だけがノートに置き換わるため、発表者用PDFの7ページ目と通常のPDFの7ページ目が対応し、並行して利用できます。

**発表用モードではなく、別の出力です。** 同じソースから投影用の通常のPDFと、フラグを有効にした発表者用のPDFを生成します。専用のコマンドラインスイッチはないため、コンパイルのたびにファイルを変更したくない場合は、自分で切り替えを実装します。

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

ノートも通常の内容と同じように記述位置の `#pause` に従います。`#pause` の後に置いたノートはそのサブスライド以降で表示されます。`subslide:` で上書きできます。

```typst
// only on subslide 2
#speaker-note(subslide: 2)[Mention the caveat here.]

// 配置場所によらず、すべてのサブスライドで表示
#speaker-note(subslide: none)[Keep an eye on the clock.]
```

ノート本文に `#pause` を記述して、サブスライドごとに内容を段階表示することもできます。

## pdfpc向けのMarkdownノート

pdfpcはノートをMarkdownとして表示します。`mode` はTouyingがノートをpdfpcファイルに書き出す形式を制御しますが、いずれも本文は通常のTypstで記述します。

```typst
#speaker-note(mode: "md")[
  - a bullet
  - *bold*
]
```

既定の `mode: "typ"` では `*bold*` がそのまま書き出されます。`mode: "md"` ではpdfpcが解釈できる `**bold**` に変換されます。見出し、リンク、強調も同様です。

## pdfpc向けの出力

既定の `enable-pdfpc: true` では、Touyingはスライド構造とともにすべてのノートをpdfpcのメタデータに記録します。PDFの横に `.pdfpc` ファイルとして書き出され、pdfpcでノート、重ね合わせの構造、タイミング情報を扱えるようになります。

ファイルの生成方法は2つあります。コンパイル後に `typst eval` を実行する方法と、バンドル出力時に `#pdfpc.bundle-assets()` を呼び出す方法です。後者は追加コマンドが不要です。発表時間、カウントダウン、画面切り替えを設定する `#pdfpc.config(..)` とともに、[pdfpc](../external/pdfpc.md) のページで説明しています。

そのページでは [Polylux](https://polylux.dev/book/external/pdfpc.html) との互換性のために残されている低レベルの `#pdfpc.speaker-note(..)` も説明しています。この関数は生の文字列を直接メタデータへ書き込みます。既存のスライドを移植する場合を除き、`#speaker-note[..]` の使用を推奨します。どちらも最終的には同じ場所に記録され、同一スライドの複数のノートは結合されます。

## パネルの装飾

セカンドスクリーンやノート専用表示のパネルは、テーマが `config-common(notes-fn: ..)` に登録する `notes` 関数によって描画されます。組み込みテーマはそれぞれこの関数を設定しているため、ノートもテーマ全体と統一された見た目になります。

テーマなしの場合は `touying-notes` が既定です。レイアウトはこの関数が担当し、装飾は `header`、`header-fill`、`fill`、`note-setting`、`preview-setting` で指定できます。独自テーマでの実装は[独自テーマを作成する](build-your-own-theme.md)で説明します。
