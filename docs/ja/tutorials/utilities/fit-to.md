---
sidebar_position: 1
---

# 高さ・幅に合わせる

コードを提供してくださった [ntjess](https://github.com/ntjess) に感謝します。

## 高さに合わせる

画像でスライドの残りの高さを埋める必要がある場合は、`fit-to-height` 関数を試すことができます。

```typst
#utils.fit-to-height(1fr)[BIG]
```

関数定義：

```typst
#let fit-to-height(
  height: 1fr,
  width: auto,
  prescale-width: none,
  grow: true,
  shrink: true,
  reflow: true,
  force-height: false,
  body,
  ..args,
) = { .. }
```

パラメーター：

- `height`：内容を合わせる高さです。デフォルトは `1fr`、つまり利用可能な残りの高さを埋めます。`length`、`fraction`、`relative` を指定できます。例えば `height: 50%` はスライドの高さの半分を占めます。
- `width`：指定した場合は、拡大縮小後の内容の幅が決まります。したがって、拡大縮小後の内容でスライドの幅の半分を埋めたい場合は、`width: 50%` を使用できます。デフォルトは `auto` です。
- `prescale-width`：このパラメーターにより、拡大縮小前に、与えられた内容が特定の幅のコンテナに配置されるものとしてTypstにレイアウトさせることができます。例えば、スライドの幅が元の2倍であると仮定するには、`prescale-width: 200%` を使用できます。
- `grow`：内容が利用可能な高さより小さいときに拡大するかどうかです。デフォルトは `true` です。
- `shrink`：内容が利用可能な高さより大きいときに縮小するかどうかです。デフォルトは `true` です。
- `reflow`：拡大縮小時にテキストの行分割をやり直すことを許可するかどうかです。デフォルトは `true` です。`width` が `auto` で、本文にテキストが含まれる場合にのみ有効になります。
- `force-height`：内容が利用可能な幅を埋めるようにする代わりに、高さ全体を強制的に占めるようにするかどうかです。デフォルトは `false` です。`reflow` が `true` で、`width` が `auto` の場合にのみ意味があります。テキストの行分割がやり直された後は、通常、できる限り幅を使う方が適切です。行数は離散的であり、使用可能な拡大縮小率も離散的になるため、高さを強制するとテキストが利用可能な幅を埋めない場合があります。
- `body`：具体的な内容です。
- `..args`：旧バージョンとの互換性のため、高さを位置引数として渡す方法も引き続きサポートされています。例えば `#utils.fit-to-height(1fr)[BIG]` です。

## 幅に合わせる

タイトルの幅をスライドの幅いっぱいに正確に収まるよう制限する必要がある場合は、`fit-to-width` 関数を試すことができます。

```typst
#utils.fit-to-width(1fr)[#lorem(20)]
```

関数定義：

```typst
#let fit-to-width(width: 1fr, grow: true, shrink: true, body, ..args) = { .. }
```

パラメーター：

- `width`：内容を合わせる幅です。デフォルトは `1fr`、つまり利用可能な残りの幅を埋めます。`length`、`fraction`、`relative` を指定できます。例えば `width: 50%` はスライドの幅の半分を占めます。
- `grow`：内容が利用可能な幅より小さいときに拡大するかどうかです。デフォルトは `true` です。
- `shrink`：内容が利用可能な幅より大きいときに縮小するかどうかです。デフォルトは `true` です。
- `body`：具体的な内容です。
- `..args`：旧バージョンとの互換性のため、幅を位置引数として渡す方法も引き続きサポートされています。例えば `#utils.fit-to-width(1fr)[#lorem(20)]` です。

## 実用例：表をスライドに収める

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide[
  #utils.fit-to-height(1fr)[
    #table(
      columns: (1fr, 1fr, 1fr),
      [A], [B], [C],
      [1], [2], [3],
      [4], [5], [6],
    )
  ]
]
```

## 見出しを幅いっぱいに表示する

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide[
  #utils.fit-to-width(1fr)[
    #text(weight: "bold")[A Very Long Presentation Title That Should Fill the Entire Slide Width]
  ]
]
```
