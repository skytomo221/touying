---
sidebar_position: 1
---

# スライドカウンターと進捗

Touyingにはプレゼンテーションの進捗を追跡・表示するカウンターとユーティリティがあります。

## スライドカウンター

`utils.slide-counter` は、スライドごとに増加する基本的なTypstのカウンターです。

```typst
// 現在のスライド番号を表示
#context utils.slide-counter.display()
```

`.get()` ではなく `.display()` を使うことに注意してください。`.get()` はカウンターを**配列**として返すため、そのまま表示すると `(1,)` のようになります。

独自のフッターでは、次のように使用します。

```example
#import "@preview/touying:0.8.0": *
#import themes.default: *

#show: default-theme.with(
  aspect-ratio: "16-9",
  config-page(
    footer: context [Slide #utils.slide-counter.display()],
  ),
)

= セクション

== 最初のスライド

ここに内容を記述します。

== 2番目のスライド

More content.
```

## スライドの総数

`utils.last-slide-number` は**付録に入る前の**最後のスライド番号を保持します。フッターの「X / Y」の分母には通常この値を使います。

```typst
#context [#utils.slide-counter.display() / #utils.last-slide-number]
```

ここでは `#context` の後に内容ブロックが必要です。`#context utils.slide-counter.display() + " / " + utils.last-slide-number` と書くと、`#context` は最初の式にしか適用されず、残りは文字列として表示されます。

## プログレスバー

`utils.touying-progress` はプレゼンテーションの進捗を0.0〜1.0の比率で返します。

```typst
#utils.touying-progress(ratio => {
  // 進捗率は0.0〜1.0の浮動小数点数
  box(width: ratio * 100%, height: 4pt, fill: primary)
})
```

`#components.progress-bar(primary, secondary, height: 2pt)` は、この機能を利用した組み込みのラッパーです。metropolis、university、stargazerの各テーマでプログレスバーの描画に使われています。

## 付録とカウンターの固定

`appendix` のshowルールは、総数を表す**分母**の `utils.last-slide-number` だけを固定します。付録のスライドは総数に含まれませんが、`utils.slide-counter` は増え続けるため、付録のフッターは `5 / 3` のようになる場合があります。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme

= 本文のセクション

== はじめに

ここでは通常どおりスライド数が増加します。

== 2番目のスライド

Still counting.

#show: appendix

= 付録

== Backup Slide

分母は引き続き本文最後のスライド数を示します。
while the slide number itself keeps advancing.
```

スライド番号も総数も増やしたくない場合は `config-common(freeze-slide-counter: true)` を使います。組み込みテーマでも `title-slide`、`outline-slide`、`focus-slide` などを番号に含めないために使われています。

```typst
#slide(config: config-common(freeze-slide-counter: true))[
  このスライドはスライドカウンターを進めません。
]
```
