---
sidebar_position: 5
---

# Pdfpc

[pdfpc](https://pdfpc.github.io/) は「PDFファイルのマルチモニター対応プレゼンターコンソール」です。つまり、PDFページの形でスライドを表示することができ、PowerPointと同様に、よく知られた優れた機能がいくつか備わっています。

pdfpcには、PDFスライドに追加情報を提供できるJSON形式の `.pdfpc` ファイルがあります。このファイルを手動で記述することもできますが、Touyingを通じて管理することもできます。
これは、ファイルにメタデータを追加し、そのメタデータを収集して `.pdfpc` ファイルへ抽出することで機能します。このような関数はすべて `pdfpc` モジュールの下からアクセスできます。

以下の内容の大部分は [Polylux](https://polylux.dev/book/external/pdfpc.html) によるものです。感謝します。

## 発表者ノート
`pdfpc.speaker-note(str|raw)` 関数を使用してスライドにノートを追加できます。このノートはpdfpcの発表者ビューでのみ表示されます。

## 終了スライド

プレゼンテーションの最後のスライドが、実際に最後に見せたいスライドとは限らないことがあります。例えば、「母と、私を信じてくれたすべての人に感謝します」というスライドの後に、完全性を期して参考文献や付録を置く場合です。

任意のスライド内にpdfpc.end-slideを記述するだけで、通常最後に表示したいスライドであることをpdfpcに伝えられます。Endキーを押すとそこへ移動します。
スライドの保存

## スライドの保存
同様に、pdfpcには特定のスライドをブックマークする機能があり（Shift + Mでそのスライドに移動できます）、Typstのソースでは、そのスライド内にpdfpc.save-slideを置くことで選択できます。
スライドを隠す

## スライドを隠す
特定のスライドを念のためプレゼンテーションに残しておきたいものの、通常は表示するつもりがない場合、pdfpc内で非表示にできます。プレゼンテーション中はスキップされますが、概要表示からは引き続き利用できます。Typstのソースでpdfpc.hidden-slideを使うと、スライドを非表示としてマークできます。

## pdfpcの設定

pdfpcの設定を追加するには、次のようにします。

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

対応する設定を追加します。具体的な設定の詳細については [Polylux](https://polylux.dev/book/external/pdfpc.html) を参照してください。


## .pdfpcファイルの出力

文書が `./example.typ` であると仮定すると、次のようにして `.pdfpc` ファイルを直接出力できます。

```sh
typst eval --in ./example.typ 'query(<pdfpc-file>).first().value' > ./example.pdfpc
```

TouyingとPolyluxの互換性により、次のコードを追加してPolyluxにも直接出力をサポートさせることができます。

```typst
#import "@preview/touying:0.8.0"

#context touying.pdfpc.pdfpc-file(here())
```

## バンドルアセットとしての.pdfpcファイルの出力

Typst 0.15以降では、1回のコンパイルでファイルのバンドル全体を出力できるため、
2回目のコマンドを実行せずに `.pdfpc` ファイルをPDFの隣に書き出せます。
`#pdfpc.bundle-assets()` の呼び出しをファイルのトップレベル、
つまりすべての `#document(..)` の外側に追加してください。

```typst
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#pdfpc.bundle-assets()

#document("deck.pdf", [
  #show: simple-theme

  == A slide
  #speaker-note[Only the speaker sees this.]
])
```

これを次のコマンドでコンパイルすると、

```sh
typst compile --features bundle --format bundle --root . ./example.typ ./out
```

`./out/deck.pdf` と `./out/deck.pdfpc` の両方が書き出されます。バンドル内の各PDF文書には、
文書名にちなんだ独自の `.pdfpc` ファイルが作られ、そのプレゼンテーションの
ノートと設定のみが含まれます。pdfpcのメタデータがない文書と
PDF以外の文書はスキップされ、バンドル出力以外ではこの呼び出しは
何も行いません。そのため、通常のPDFにもコンパイルするファイルに `#pdfpc.bundle-assets()` を
残しておくことができます。ただし、これは呼び出し自体についての話です。
上記の `#document(..)` によるラッパーはバンドル専用であり、Typstは
`constructing a document is only supported in the bundle target` というエラーを出して、
PDFへのコンパイル時にこれを拒否します。

バンドル出力はまだ実験的な機能であることに注意してください。そのためTypstは警告を表示し、
その動作は変更される可能性があります。
