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

== Prime numbers

#definition[
  A natural number is called a #highlight[_prime number_] if it is greater
  than 1 and cannot be written as the product of two smaller natural numbers.
]
#example[
  $2$、$3$、$17$ は素数です。
  @cor_largest_prime shows that this list is not exhaustive!
]

#pause

#theorem(title: "Euclid")[
  There are infinitely many primes.
]

#pagebreak(weak: true)

#proof[
  Suppose to the contrary that $p_1, p_2, dots, p_n$ is a finite enumeration
  of all primes. Set $P = p_1 p_2 dots p_n$. Since $P + 1$ is not in our list,
  it cannot be prime. Thus, some prime factor $p_j$ divides $P + 1$. Since
  $p_j$ also divides $P$, it must divide the difference $(P + 1) - P = 1$, a
  contradiction.
]

#corollary[
  There is no largest prime number.
] <cor_largest_prime>
#corollary[
  There are infinitely many composite numbers.
]

#theorem[
  There are arbitrarily long stretches of composite numbers.
]

#proof[
  For any $n > 2$, consider $
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
