---
sidebar_position: 2
---

# Touying Exporter

[touying-exporter](https://github.com/touying-typ/touying-exporter) は、Touyingのプレゼンテーションを各種形式へ出力するコマンドラインツールです。Touying向けに設計されていますが、ほかのTypstファイルにも使用できます。

編集機能と発表機能を備え、編集可能なPPTXへの実験的な出力を行いたい場合は [Tylina](./tylina.md) を推奨します。コマンドラインでの自動化やHTML出力にはtouying-exporterを使用してください。

## Touyingテンプレート

[Touyingテンプレート](https://github.com/touying-typ/touying-template) を使うと、GitHub Pagesでオンラインプレゼンテーションを公開できます。

デモ：https://touying-typ.github.io/touying-template/

テンプレートの使用手順は次のとおりです。

1. `Use this template` ボタンでリポジトリを複製します。
2. `Settings -> Pages -> Branch -> None -> gh-pages -> Save` を選んでGitHub Pagesを有効にします。
3. `your-name.github.io/repo-name` を開いてプレゼンテーションを開始します。

制約として、文字を選択してコピーすることはできません。必要な場合は [Gistd](https://github.com/Myriad-Dreamin/gistd) を使用してください。

## HTMLへの出力

SVG画像を生成し、impress.jsとともにHTMLファイルへまとめます。ブラウザで開いて発表でき、GIFアニメーションや発表者ノートにも対応します。

![image](https://github.com/touying-typ/touying-exporter/assets/34951714/207ddffc-87c8-4976-9bf4-4c6c5e2573ea)

![image](https://github.com/touying-typ/touying-exporter/assets/34951714/eac4976b-7d5d-40b6-8827-88c9a024b89a)

オンライン発表用の [Touyingテンプレート](https://github.com/touying-typ/touying-template)。[オンラインデモ](https://touying-typ.github.io/touying-template/)。

## PPTXへの出力

PNG画像を生成してPPTXファイルにまとめます。PowerPointで開いて発表でき、発表者ノートにも対応します。

![image](https://github.com/touying-typ/touying-exporter/assets/34951714/3d547c74-fb4b-4c31-81e5-5138a5d727c9)

## インストール

```sh
pip install touying
```


## コマンドラインの使い方

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

たとえば次のようにします。

```sh
touying compile example.typ
```

`example.html` が生成されます。ブラウザで開けば発表を開始できます。

### Typstへ変数を渡す

`--sys-inputs` パラメーターでTypstファイルに変数を渡せます。

```sh
touying compile example.typ --sys-inputs '{"title":"My Presentation","author":"John Doe"}'
```

Typstファイル側では次のように参照します。

```typst
#let title = sys.inputs.at("title", default: "Default Title")
#let author = sys.inputs.at("author", default: "Default Author")

= #title
By #author
```


### `utils.get-input` で入力を読み取る

`--sys-inputs`（または `typst compile --input key=value`）で渡された値は、Typstでは文字列として受け取られます。`sys.inputs.at(..)` はそのまま文字列を返します。Touyingの `utils.get-input` は値をTypstの構文として解析します。Touying文書でコマンドライン入力を読み取るために推奨される方法であり、Touying自身も同じ関数で設定の上書きを読み取ります。

```typst
#import "@preview/touying:0.8.0": *

// a single value, or `none` when the key was not passed
#let accent = utils.get-input(key: "accent")

// or the whole dictionary, with every value parsed
#let inputs = utils.get-input()
```

Typstが認識できる値はTypstの構文として解析されます。

| コマンドラインで指定した値 | `utils.get-input(key: ..)` の返り値 |
| --- | --- |
| `accent=red` | 色の `red` |
| `pos=left` | 配置の `left` |
| `size=2em`, `count=3` | 長さの `2em`、整数の `3` |
| `flag=true` | 真偽値の `true`。`false`、`none`、`auto` も同様です。 |
| `config='(a: 1, b: (2, 3))'` | 辞書 |
| `mode=handout` | 文字列の `"handout"` |

最後の行がシェルでの使いやすさにつながります。Typstが認識しない単独の単語（英数字、`_`、`-`）はそのまま文字列として返されるため、通常の値を引用符で囲む必要はありません。指定されなかったキーは `none` を返します。

次の2点に注意してください。

- Typstの識別子として認識される単語は文字列ではなく解析されます。`name=red` は文字列の `"red"` ではなく色です。文字列にしたい場合は `name='"red"'` とします。
- 単独の単語でも正しいTypstコードでもない値は、文字列にはならずコンパイルエラーになります。`title=My Presentation` は空白があるため失敗します。`title='"My Presentation"'` のように値に引用符を含めるか、自由なテキストの場合は `sys.inputs` を直接読み取ってください。

`touying compile` でも同様です。`--sys-inputs` のJSON値はそのままTypstに渡される文字列です。

```sh
touying compile example.typ --sys-inputs '{"accent":"red","title":"\"My Presentation\""}'
```


## Pythonパッケージとして使用する

```python
import touying

touying.to_html("example.typ")
```
