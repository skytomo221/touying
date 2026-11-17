---
sidebar_position: 6
---

# Theorion

Touyingは [Theorion](https://github.com/OrangeX4/typst-theorion) パッケージと連携できます。また、`#set heading(numbering: "1.1")` でセクションやサブセクションの番号を設定できます。

**注意：Theorionと併用して `#pause` などのアニメーションを正しく動作させるには、`config-common(frozen-counters: (theorem-counter,))` を使い、固定対象のカウンターを指定してください。**

```example
#import "@preview/touying:0.8.0": *
#import themes.university: *
#import "@preview/numbly:0.1.0": numbly
#import "@preview/theorion:0.6.0": *
#import cosmos.clouds: *
#show: show-theorion

#show: university-theme.with(
  aspect-ratio: "16-9",
  config-common(frozen-counters: (theorem-counter,)),  // アニメーション中は定理カウンターを固定
)

#set heading(numbering: numbly("{1}.", default: "1.1"))

= 定理

== 素数

#definition[
  1より大きく、より小さい2つの自然数の積として表せない自然数を
  #highlight[_素数_] と呼びます。
]
#example[
  $2$、$3$、$17$ は素数です。
  @cor_largest_prime から、この一覧がすべてではないとわかります。
]

#pause

#theorem(title: "Euclid")[
  素数は無限に存在します。
]

#pagebreak(weak: true)

#proof[
  背理法で、すべての素数が $p_1, p_2, dots, p_n$ の有限個だと仮定します。
  $P = p_1 p_2 dots p_n$ とおきます。$P + 1$ は一覧にないため素数ではありません。
  したがって、ある素因数 $p_j$ が $P + 1$ を割ります。しかし $p_j$ は $P$ も割るので、
  差 $(P + 1) - P = 1$ も割ることになり、矛盾します。
]

#corollary[
  最大の素数は存在しません。
] <cor_largest_prime>
#corollary[
  合成数は無限に存在します。
]

#theorem[
  任意の長さだけ連続する合成数が存在します。
]

#proof[
  任意の $n > 2$ について、次を考えます。$
    n! + 2, quad n! + 3, quad ..., quad n! + n
  $
]
```
## `frozen-counters` が必要な理由

Touyingはサブスライドごとに内容を再評価します。`frozen-counters` がなければ、Theorion内部の `theorem-counter` がサブスライドごとに増加し、定理番号が意図せず飛んでしまいます。

`config-common(frozen-counters: (theorem-counter,))` により、Touyingはスライド開始時のカウンター値を保存し、各サブスライドの描画前に復元します。これでアニメーションの各段階で定理番号が一貫します。

## 複数種類のカウンター

図のカウンターも固定する場合は、次のように設定します。

```typst
config-common(frozen-counters: (theorem-counter, counter(figure.where(kind: image))))
```

## Cosmosのスタイル

Theorionには複数の表示スタイルがあります。上の例では `cosmos.clouds` を使用していますが、`cosmos.fancy` や `cosmos.aurora` なども利用できます。詳しくは [Theorionのドキュメント](https://github.com/OrangeX4/typst-theorion)を参照してください。
