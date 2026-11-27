---
sidebar_position: 5
---

# Codly

[Codly](https://github.com/Dherse/codly) は、言語アイコン、行番号、構文ハイライトを備えた美しいコードブロックを提供するTypstパッケージです。

## セットアップ

Touyingはサブスライドごとに内容を再描画するため、各スライドを描画する前に `codly` のページ単位の状態を復元する必要があります。Codlyの初期化処理を `preamble` として渡します。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *
#import "@preview/codly:1.3.0": *
#import "@preview/codly-languages:0.1.10": *

#show: codly-init.with()

#show: simple-theme.with(
  aspect-ratio: "16-9",
  config-common(preamble: {
    codly(languages: codly-languages)
  }),
)

== First Slide

#raw(lang: "rust", block: true,
`pub fn main() {
  println!("Hello, world!");
}`.text)
```

## コードブロックのアニメーション

`#pause` や `#only` を使えば、コードを1行ずつ表示できます。ただし、`#pause` は `raw` ブロック内では直接機能しないことに注意してください。アニメーション付きのコードには `touying-raw` を使用します。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(aspect-ratio: "16-9")

== Animated Raw Block

#touying-raw(lang: "python", ```
print("Step 1")
// pause
print("Step 2")
// pause
print("Step 3")
```)
```

:::tip

`touying-raw` は特別なコメントマーカー（`// pause`、`// meanwhile`）を使用してアニメーションの各ステップを開始し、ソースコードの読みやすさを保ちます。

:::

## Codlyとアニメーション

Codlyで完全にスタイルが適用されたアニメーション付きのブロックを作るには、`touying-raw` と `config-common(preamble: ...)` を組み合わせます。

```typst
#show: simple-theme.with(
  aspect-ratio: "16-9",
  config-common(preamble: {
    codly(languages: codly-languages)
  }),
)

== Animated Code

#touying-raw(lang: "python", ```
def greet(name):
// pause
    return f"Hello, {name}!"
// pause
print(greet("World"))
```)
```
