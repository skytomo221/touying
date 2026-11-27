---
sidebar_position: 1
---

# スライドカウンターと進捗

Touyingは、プレゼンテーションの進捗を追跡して表示するための一連のカウンターとユーティリティを提供します。

## スライドカウンター

`utils.slide-counter` は、スライドごとに増加する主要なTypstカウンターです。

```typst
// 現在のスライド番号を表示する
#context utils.slide-counter.display()
```

`.display()` を使用し、`.get()` を使用していないことに注意してください。`.get()` はカウンターを**配列**形式で返すため、コンテンツに直接配置すると `(1,)` と描画されます。

カスタムフッターでは次のように使用します。

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

`utils.last-slide-number` は**付録より前の**最後のスライドの番号を保持します。通常、「全Y枚中のX枚目」というフッターの分母として表示したいのは、この値です。

```typst
#context [#utils.slide-counter.display() / #utils.last-slide-number]
```

ここでの `#context` の後にはコンテンツブロックが必要です。`#context utils.slide-counter.display() + " / " + utils.last-slide-number` と記述すると、マークアップ内の `#context` は最初の式にしか結び付かず、残りはそのままテキストとして出力されます。

## プログレスバー

`utils.touying-progress` は、プレゼンテーションがどこまで進んでいるかを表す比率（0.0〜1.0）を提供します。

```typst
#utils.touying-progress(ratio => {
  // ratioは0.0から1.0の間の浮動小数点数
  box(width: ratio * 100%, height: 4pt, fill: primary)
})
```

`#components.progress-bar(primary, secondary, height: 2pt)` はそのための既成のラッパーです。metropolis、university、stargazerテーマはこの方法でプログレスバーを描画します。

## 付録とカウンターの固定

`appendix` のshowルールは**分母**、つまりスライドの総数である `utils.last-slide-number` のみを固定します。そのため、付録のスライドによってフッターに表示される総数は変化しません。スライドカウンター `utils.slide-counter` は進み続けるため、付録のフッターには `5 / 3` のように表示されます。

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

スライドをカウントの対象から完全に除外したい場合（スライド番号も総数も増加させない場合）は、`config-common(freeze-slide-counter: true)` を使用します。同梱されているテーマが `title-slide`、`outline-slide`、`focus-slide` などのスライドにスライド番号を消費させないようにしているのは、まさにこの方法です。

```typst
#slide(config: config-common(freeze-slide-counter: true))[
  This slide does not advance the slide counter.
]
```
