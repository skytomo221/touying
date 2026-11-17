---
sidebar_position: 1
---

# 高さ・幅に合わせる

このコードを提供してくれた [ntjess](https://github.com/ntjess) に感謝します。

## 高さに合わせる

画像などの内容をスライドの残りの高さに合わせたい場合は、`fit-to-height` 関数を使用します。

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

- `height`：内容を合わせる高さ。既定値は残りの高さを使う `1fr` です。`length`、`fraction`、`relative` を指定でき、たとえば `height: 50%` はスライドの高さの半分になります。
- `width`：拡大縮小後の幅。スライド幅の半分に収めるなら `width: 50%` を指定します。既定値は `auto` です。
- `prescale-width`：拡大縮小の前に、指定した幅の領域へ内容を配置するものとしてTypstにレイアウトさせます。たとえばスライド幅の2倍の領域を想定するには `prescale-width: 200%` とします。
- `grow`：利用可能な高さより内容が小さいときに拡大するかどうか。既定値は `true` です。
- `shrink`：利用可能な高さより内容が大きいときに縮小するかどうか。既定値は `true` です。
- `reflow`：拡大縮小時にテキストの改行位置を再計算するかどうか。既定値は `true` で、`width` が `auto` かつ本文にテキストがある場合にのみ有効です。
- `force-height`：利用可能な幅ではなく高さ全体を優先して使用するかどうか。既定値は `false` です。`reflow` が `true` かつ `width` が `auto` の場合に意味があります。テキストの行数は離散的なので、高さを強制すると幅に余白が残ることがあります。
- `body`：対象となる内容。
- `..args`：旧バージョンとの互換性のため、`#utils.fit-to-height(1fr)[BIG]` のように高さを位置引数で指定する方法もサポートされています。

## 幅に合わせる

見出しなどの内容をスライドの幅いっぱいに収めたい場合は、`fit-to-width` 関数を使います。

```typst
#utils.fit-to-width(1fr)[#lorem(20)]
```

関数定義：

```typst
#let fit-to-width(width: 1fr, grow: true, shrink: true, body, ..args) = { .. }
```

パラメーター：

- `width`：内容を合わせる幅。既定値は残りの幅を使う `1fr` です。`length`、`fraction`、`relative` を指定でき、`width: 50%` ならスライド幅の半分になります。
- `grow`：利用可能な幅より内容が小さい場合に拡大するかどうか。既定値は `true` です。
- `shrink`：利用可能な幅より内容が大きい場合に縮小するかどうか。既定値は `true` です。
- `body`：対象となる内容。
- `..args`：旧バージョンとの互換性のため、`#utils.fit-to-width(1fr)[#lorem(20)]` のように幅を位置引数として指定することもできます。

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
