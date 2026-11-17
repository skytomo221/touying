---
sidebar_position: 1
---

# スライドカウンターと進捗

Touyingにはプレゼンテーションの進捗を追跡・表示するカウンターとユーティリティがあります。

## スライドカウンター

`utils.slide-counter` は、スライドごとに増加する基本的なTypstのカウンターです。

```typst
// Display the current slide number
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

= Section

== First Slide

Content here.

== Second Slide

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
  // ratio is a float between 0.0 and 1.0
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

= Main Section

== Introduction

The slide count increments normally here.

== Second Slide

Still counting.

#show: appendix

= Appendix

== Backup Slide

The denominator still shows the count from the last main slide,
while the slide number itself keeps advancing.
```

スライド番号も総数も増やしたくない場合は `config-common(freeze-slide-counter: true)` を使います。組み込みテーマでも `title-slide`、`outline-slide`、`focus-slide` などを番号に含めないために使われています。

```typst
#slide(config: config-common(freeze-slide-counter: true))[
  This slide does not advance the slide counter.
]
```
