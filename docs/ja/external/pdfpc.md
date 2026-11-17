---
sidebar_position: 5
---

# Pdfpc

[pdfpc](https://pdfpc.github.io/) はPDFのマルチモニター表示に対応したプレゼンターコンソールです。PowerPointのように便利な機能を備え、PDFのページをスライドとして発表できます。

pdfpcはJSON形式の `.pdfpc` ファイルでPDFスライドの追加情報を扱います。手動作成も可能ですが、Touyingからも管理できます。文書にメタデータを追加し、それを収集して `.pdfpc` ファイルとして出力する仕組みです。関連機能は `pdfpc` モジュールにあります。

以下の内容の多くは [Polylux](https://polylux.dev/book/external/pdfpc.html) を参考にしています。

## 発表者ノート
`pdfpc.speaker-note(str|raw)` を使うと、pdfpcの発表者ビューにのみ表示されるノートをスライドへ追加できます。

## 終了スライド

プレゼンテーションの最後のページが、実際の発表で最後に見せたいスライドとは限りません。たとえば、謝辞の後に参考文献や付録を配置する場合があります。

任意のスライド内に `pdfpc.end-slide` を置くと、そのスライドを通常の発表の終了地点として設定できます。Endキーを押すとその位置へ移動します。

## スライドの保存
pdfpcには特定のスライドをブックマークし、Shift + Mで移動する機能もあります。Typstのソースでは、対象のスライド内に `pdfpc.save-slide` を配置します。

## スライドを隠す
予備のスライドを残しつつ通常の発表では見せたくない場合は、pdfpcで非表示にできます。発表中はスキップされますが、一覧からはアクセスできます。Typstのソースでは `pdfpc.hidden-slide` で指定します。

## pdfpcの設定

pdfpcの設定は次のように追加します。

```typst
#pdfpc.config(
  duration-minutes: 30,
  start-time: datetime(hour: 14, minute: 10, second: 0),
  end-time: datetime(hour: 14, minute: 40, second: 0),
  last-minutes: 5,
  note-font-size: 12,
  disable-markdown: false,
  default-transition: (
    type: "push",
    duration-seconds: 2,
    angle: ltr,
    alignment: "vertical",
    direction: "inward",
  ),
)
```

必要な設定を追加してください。詳しくは [Polylux](https://polylux.dev/book/external/pdfpc.html) を参照してください。


## .pdfpcファイルの出力

文書が `./example.typ` の場合、次のコマンドで `.pdfpc` ファイルを直接出力できます。

```sh
typst eval --in ./example.typ 'query(<pdfpc-file>).first().value' > ./example.pdfpc
```

TouyingとPolyluxの互換性を利用し、次のコードを追加すればPolyluxからも直接出力できます。

```typst
#import "@preview/touying:0.8.0"

#context touying.pdfpc.pdfpc-file(here())
```

## .pdfpcファイルの出力s as Bundle Assets

Typst 0.15以降は、1回のコンパイルで複数ファイルをまとめたバンドルを生成できます。そのため、別のコマンドなしでPDFと同じ場所に `.pdfpc` ファイルを出力できます。各 `#document(..)` の外側、ファイルのトップレベルに `#pdfpc.bundle-assets()` を追加してください。

```typst
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#pdfpc.bundle-assets()

#document("deck.pdf", [
  #show: simple-theme

  == A slide
  #speaker-note[これは発表者だけに見えます。]
])
```

次のコマンドでコンパイルすると、

```sh
typst compile --features bundle --format bundle --root . ./example.typ ./out
```

`./out/deck.pdf` と `./out/deck.pdfpc` が生成されます。バンドル内の各PDFには、その文書名に対応し、そのプレゼンテーションのノートと設定だけを含む `.pdfpc` ファイルが作られます。pdfpcのメタデータがない文書とPDF以外の文書は対象外です。通常のPDF出力時には `#pdfpc.bundle-assets()` 自体は何もしないため、そのまま残せます。ただし、上の `#document(..)` によるラッパーはバンドル専用で、通常のPDFコンパイルでは `constructing a document is only supported in the bundle target` というエラーになります。

バンドル出力はまだ実験的な機能です。Typstから警告が表示され、今後動作が変更される可能性があります。
