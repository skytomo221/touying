---
sidebar_position: 6
---

# Theorion

Touyingは [Theorion](https://github.com/OrangeX4/typst-theorion) パッケージと正しく連携できるため、[theorion](https://github.com/OrangeX4/typst-theorion) パッケージを直接使用できます。さらに、`#set heading(numbering: "1.1")` を使ってセクションとサブセクションの番号付けを設定できます。

**注意：theorionと併用して `#pause` などのアニメーションコマンドを正しく動作させるには、`config-common(frozen-counters: (theorem-counter,))` を使って、固定する必要があるカウンターをバインドする必要があります。**

```example
#import "@preview/touying:0.8.0": *
#import themes.university: *
#import "@preview/numbly:0.1.0": numbly
#import "@preview/theorion:0.6.0": *
#import cosmos.clouds: *
#show: show-theorion

#show: university-theme.with(
  aspect-ratio: "16-9",
  config-common(frozen-counters: (theorem-counter,)),  // アニメーションのために定理カウンターを固定する
)

#set heading(numbering: numbly("{1}.", default: "1.1"))

= Theorems

== Prime numbers

#definition[
  A natural number is called a #highlight[_prime number_] if it is greater
  than 1 and cannot be written as the product of two smaller natural numbers.
]
#example[
  The numbers $2$, $3$, and $17$ are prime.
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

Touyingはスライドの内容を再評価することで各サブスライドを描画します。`frozen-counters` がなければ、Theorion内部の `theorem-counter` がサブスライドごとに増加し、定理番号が予期せず飛んでしまいます。

`config-common(frozen-counters: (theorem-counter,))` は、各スライドの開始時にカウンターの値を記録し、各サブスライドを描画する前にその値を復元するようTouyingに指示します。これにより、アニメーションの各ステップで定理番号が一貫します。

## 複数種類のカウンター

固定すべき図のカウンターもある場合：

```typst
config-common(frozen-counters: (theorem-counter, counter(figure.where(kind: image))))
```

## Cosmosのスタイル

Theorionには複数の視覚的なスタイルが同梱されています。上の例では `cosmos.clouds` を使用しています。ほかにも `cosmos.fancy`、`cosmos.aurora` などがあります。詳細は [Theorionのドキュメント](https://github.com/OrangeX4/typst-theorion) を参照してください。
