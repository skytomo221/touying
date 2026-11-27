---
sidebar_position: 3
---

# 数式のアニメーション

数式もほかの内容と同様にアニメーション化できます。`#pause`、`#meanwhile`、`#only`、`#uncover`、`#alternatives` を `$ .. $` の中に直接記述します。数式モード内では、ハッシュ記号なしの単独の `pause` も機能します。

## シンプルなアニメーション

まず例から始めましょう。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide[
  Equation with pause:

  $
    f(x) &= pause x^2 + 2x + 1  \
         &= pause (x + 1)^2  \
  $

  #meanwhile

  Animating an equation is very simple.
]
```

数式が段階的に表示されるため、導出過程を1行ずつ発表するのに適しています。

## 複雑なアニメーション

`only`、`uncover`、`effect`、`alternatives` は数式内でも機能します。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide[
  $
    f(x) &= #pause x^2 + 2x + #uncover("3-")[1]  \
         &= #pause (x + 1)^2  \
  $
]
```

## `frac`、`mat` などの数式要素の内部

アニメーションは、内容を独自のフィールドに保持する数式要素の内部にも作用します。
例えば `frac`、`mat`、`vec`、`cases`、`binom`、`root`、`attach`、
`accent` や波括弧系の要素などです。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide[
  $ frac(a #pause + b, c) $

  $ mat(a, #pause b; c, d) $

  $ a^(2 #pause + 1) $
]
```

唯一の例外はアクセントの*アクセント記号*の引数（`hat`、つまり `accent(x, hat)` 内のもの）です。Typstはこれを内容ではなく単一の記号として格納するため、その場でアニメーション化することはできません。代わりに要素全体を切り替えてください。

```typst
#alternatives($accent(x, hat)$, $accent(x, tilde)$)
```

## アニメーション化した内容への参照

Typstで `@label` を記述できるのは、図、数式、
見出し、脚注という限られた種類の要素だけです。それ以外のものは図で囲む必要があります。したがって、アニメーション化したコードブロックやリデューサーによる図を参照するには、図で囲みます。

```typst
#figure(
  touying-raw(```py
x = 1
# pause
y = 2
```),
)<my-code>

See @my-code.
```

図によってキャプションと `kind` も得られます。rawブロックに直接付けたラベルも
`#link(<my-code>)[jump there]` では機能します。このリンクには図は必要ありません。

アニメーション化した内容のラベルは各サブスライドで繰り返されるべきではありません。`config-common(label-only-on-last-subslide: ..)` は、
どの要素関数のラベルについて重複を排除するかを制御します。
