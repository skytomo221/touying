---
sidebar_position: 1
---

# Pinit

[Pinit](https://github.com/OrangeX4/typst-pinit/) はページを基準とした絶対配置や、ピンを基準とした相対配置を実現するパッケージです。スライド上で矢印や注釈を使った説明をするときに便利です。

## 基本的な例

```example
#import "@preview/pinit:0.2.2": *

#set text(size: 24pt)

A simple #pin(1)highlighted text#pin(2).

#pinit-highlight(1, 2)

#pinit-point-from(2)[It is simple.]
```

別の[例](https://github.com/OrangeX4/typst-pinit/blob/main/examples/equation-desc.typ)：

![image](https://github.com/touying-typ/touying/assets/34951714/9b4a6b50-fcfd-497d-9649-ae1f7762ee3f)


## 高度な例

Touyingと組み合わせた使用例：

```example
#import "@preview/touying:0.8.0": *
#import themes.default: *
#import "@preview/pinit:0.2.2": *

#set text(size: 20pt, font: "Calibri", ligatures: false)
#show heading: set text(weight: "regular")
#show heading: set block(above: 1.4em, below: 1em)
#show heading.where(level: 1): set text(size: 1.5em)

// 便利な関数
#let crimson = rgb("#c00000")
#let greybox(..args, body) = rect(fill: luma(95%), stroke: 0.5pt, inset: 0pt, outset: 10pt, ..args, body)
#let redbold(body) = {
  set text(fill: crimson, weight: "bold")
  body
}
#let blueit(body) = {
  set text(fill: blue)
  body
}

#show: default-theme.with(aspect-ratio: "4-3")

// 本文
#slide[
  #set heading(offset: 0)

  = 漸近記法: $O$

  アルゴリズムの漸近的な効率を説明するには #pin("h1")漸近記法#pin("h2") を使います。
  （定数係数と低次の項は無視します。）

  #pause

  #greybox[
    関数 $g(n)$ に対して、次の*関数の集合*を $O(g(n))$ と表します。
    #redbold(${f(n): "exists" c > 0 "and" n_0 > 0, "such that" f(n) <= c dot g(n) "for all" n >= n_0}$)
  ]

  #pinit-highlight("h1", "h2")

  #pause

  $f(n) = O(g(n))$：#pin(1)$f(n)$ は $g(n)$ より*漸近的に小さい*関数です。#pin(2)

  #pause

  $f(n) redbold(in) O(g(n))$：$f(n)$ は*漸近的に* $g(n)$ を#redbold[超えません]。

  #only("4-", pinit-line(stroke: 3pt + crimson, start-dy: -0.25em, end-dy: -0.25em, 1, 2))

  #pause

  #block[#pin("r1")例#pin("r2")として挿入ソートを考えます：]

  - 最良の場合: $T(n) approx c n + c' n - c''$ #pin(3)
  - 最悪の場合: $T(n) approx c n + (c' \/ 2) n^2 - c''$ #pin(4)

  #pinit-rect("r1", "r2")

  #pause

  #pinit-place(3, dx: 15pt, dy: -15pt)[#redbold[$T(n) = O(n)$]]
  #pinit-place(4, dx: 15pt, dy: -15pt)[#redbold[$T(n) = O(n)$]]

  #pause

  #blueit[問：$n^(3) = O(n^2)$#pin("que") は成り立ちますか？ 答えをどう証明しますか#pin("ans")？]

  #pause

  #pinit-point-to("que", fill: crimson, redbold[いいえ。])
  #pinit-point-from("ans", body-dx: -150pt)[
    不等式 $(3/2)^n >= c$ を満たす $n$ が \
    無限に存在することを示してください。
  ]
]
```
