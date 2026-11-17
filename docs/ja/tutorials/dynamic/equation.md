---
sidebar_position: 3
---

# 数式のアニメーション

数式も通常の内容と同様にアニメーション化できます。`$ .. ---
sidebar_position: 3
---

# 数式のアニメーション

 の中に `#pause`、`#meanwhile`、`#only`、`#uncover`、`#alternatives` を記述します。数式モードでは `#` なしの `pause` も使えます。

## シンプルなアニメーション

まず例を見てください。

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

数式が段階的に表示されるため、導出過程を1行ずつ説明するのに適しています。

## 複雑なアニメーション

数式の内部でも `only`、`uncover`、`effect`、`alternatives` が使えます。

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

`frac`、`mat`、`vec`、`cases`、`binom`、`root`、`attach`、`accent`、括弧関数など、内容を専用のフィールドに保持する数式要素の内部もアニメーション化できます。

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

例外はアクセントの引数（`accent(x, hat)` の `hat`）です。Typstではこれを内容ではなく単一の記号として保持するため、直接アニメーション化できません。代わりに要素全体を切り替えます。

```typst
#alternatives($accent(x, hat)$, $accent(x, tilde)$)
```

## アニメーション化した内容への参照

Typstで `@label` による参照ができるのは図、数式、見出し、脚注など一部の要素です。その他の内容を参照するには `figure` で囲む必要があります。アニメーション化したコードブロックや図を参照する場合は次のようにします。

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

`figure` を使うとキャプションと `kind` も利用できます。一方、`#link(<my-code>)[jump there]` のようなリンクでは `figure` がなくてもrawブロックへのラベルが使えます。

アニメーション化した内容のラベルは各サブスライドで重複させないようにします。`config-common(label-only-on-last-subslide: ..)` でラベルの重複を取り除く対象を指定できます。
