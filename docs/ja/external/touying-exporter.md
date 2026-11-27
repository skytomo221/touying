---
sidebar_position: 2
---

# Touying Exporter

[touying-exporter](https://github.com/touying-typ/touying-exporter) は、Touyingのプレゼンテーションをさまざまな形式に出力するコマンドラインツールです。Touyingのプレゼンテーションで使用するように設計されていますが、ほかのTypstファイルにも使用できます。Touying向けにプレゼンテーションのスライドをさまざまな形式で出力します。

インタラクティブなエディター兼発表ツールと、実験的な編集可能PPTX出力が必要な場合は、[Tylina](./tylina.md) を推奨します。コマンドラインによる自動化やHTML出力が必要な場合はtouying-exporterを使用してください。

## Touyingテンプレート

GitHub Pagesでオンラインプレゼンテーションを行うための [Touyingテンプレート](https://github.com/touying-typ/touying-template) です。

デモ: https://touying-typ.github.io/touying-template/

このテンプレートを使用するには、次の手順に従ってください。

1. `Use this template` ボタンをクリックしてリポジトリをコピーします。
2. `Settings -> Pages -> Branch -> None -> gh-pages -> Save` をクリックしてGitHub Pagesを有効にします。
3. リンク `your-name.github.io/repo-name` を開いてプレゼンテーションを開始します。

欠点：テキストを選択してコピーすることはできません。必要な場合は [Gistd](https://github.com/Myriad-Dreamin/gistd) を使用してください。

## HTMLへの出力

SVG画像ファイルを生成し、impress.jsと一緒にHTMLファイルにまとめます。このようにしてブラウザで開いて発表することができ、GIFアニメーションと発表者ノートにも対応しています。

![image](https://github.com/touying-typ/touying-exporter/assets/34951714/207ddffc-87c8-4976-9bf4-4c6c5e2573ea)

![image](https://github.com/touying-typ/touying-exporter/assets/34951714/eac4976b-7d5d-40b6-8827-88c9a024b89a)

オンラインプレゼンテーション用の [Touyingテンプレート](https://github.com/touying-typ/touying-template) です。[オンライン](https://touying-typ.github.io/touying-template/)

## PPTXへの出力

PNG画像ファイルを生成し、PPTXファイルにまとめます。このようにしてPowerPointで開いて発表することができ、発表者ノートにも対応しています。

![image](https://github.com/touying-typ/touying-exporter/assets/34951714/3d547c74-fb4b-4c31-81e5-5138a5d727c9)

## インストール

```sh
pip install touying
```


## CLI

```text
usage: touying compile [-h] [--output OUTPUT] [--root ROOT] [--font-paths [FONT_PATHS ...]] [--start-page START_PAGE] [--count COUNT] [--ppi PPI] [--silent SILENT] [--format {html,pptx,pdf,pdfpc}] [--sys-inputs SYS_INPUTS] input

positional arguments:
  input                 Input file

options:
  -h, --help            show this help message and exit
  --output OUTPUT       Output file
  --root ROOT           Root directory for typst file
  --font-paths [FONT_PATHS ...]
                        Paths to custom fonts
  --start-page START_PAGE
                        Page to start from
  --count COUNT         Number of pages to convert
  --ppi PPI             Pixels per inch for PPTX format
  --silent SILENT       Run silently
  --format {html,pptx,pdf,pdfpc}
                        Output format
  --sys-inputs SYS_INPUTS
                        JSON string to pass to typst's sys.inputs
```

例えば：

```sh
touying compile example.typ
```

`example.html` ファイルが生成されます。ブラウザで開き、プレゼンテーションを開始しましょう :-)

### Typstに変数を渡す

`--sys-inputs` パラメーターを使ってTypstファイルに変数を渡すことができます。

```sh
touying compile example.typ --sys-inputs '{"title":"My Presentation","author":"John Doe"}'
```

Typstファイル内では、次のようにこれらの変数にアクセスできます。

```typst
#let title = sys.inputs.at("title", default: "Default Title")
#let author = sys.inputs.at("author", default: "Default Author")

= #title
By #author
```


### `utils.get-input` で入力を読み取る

`--sys-inputs`（または `typst compile --input key=value`）を通して渡されたすべての値は、
Typstに文字列として届き、`sys.inputs.at(..)` はその文字列を変更せずに返します。Touyingは
`utils.get-input` を提供しており、まず各値をTypstとして解析します。これは
Touying文書でコマンドライン入力を読み取るためにサポートされている方法です。Touying自身も
コマンドラインによる上書き設定をまさにこの関数で読み取ります。

```typst
#import "@preview/touying:0.8.0": *

// 単一の値、またはキーが渡されていない場合は `none`
#let accent = utils.get-input(key: "accent")

// または、すべての値を解析した辞書全体
#let inputs = utils.get-input()
```

Typstが認識できるものはすべてTypstとして解析されます。

| コマンドラインで指定した値 | `utils.get-input(key: ..)` の戻り値 |
| --- | --- |
| `accent=red` | 色の `red` |
| `pos=left` | 配置の `left` |
| `size=2em`, `count=3` | 長さの `2em`、整数の `3` |
| `flag=true` | `true`。`false`、`none`、`auto` も同様 |
| `config='(a: 1, b: (2, 3))'` | 辞書 |
| `mode=handout` | 文字列の `"handout"` |

最後の行こそがシェルからの使いやすさにつながります。Typstが認識しない単独の引用符なしの単語、
すなわち文字、数字、`_`、`-` はそのまま文字列として返されるため、通常の
値を引用符で囲む必要はありません。渡されなかったキーからは `none` が得られます。

次の2点に注意してください。

- Typstのバインディングで*ある*引用符なしの単語は、テキストとして維持されず解析されます。`name=red` は色であり、
  文字列の `"red"` ではありません。文字列が必要な場合は `name='"red"'` と記述してください。
- 引用符なしの単語でも有効なTypstコードでもない値は文字列ではなくコンパイルエラーになります。
  `title=My Presentation` は空白の箇所で失敗します。値の一部として引用符を渡すようにして、
  `title='"My Presentation"'` とするか、自由形式のテキストには `sys.inputs` を直接読み取ってください。

`touying compile` でも同様です。このコマンドの `--sys-inputs` のJSON値は、
Typstが受け取る生のテキストです。

```sh
touying compile example.typ --sys-inputs '{"accent":"red","title":"\"My Presentation\""}'
```


## Pythonパッケージとして使用する

```python
import touying

touying.to_html("example.typ")
```
