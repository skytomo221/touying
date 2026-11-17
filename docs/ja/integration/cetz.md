---
sidebar_position: 3
---

# CeTZ

Touyingは同義の関数 `touying-diagram` と `touying-reduce` を提供しており、CeTZの図に `pause` や `meanwhile` などのアニメーションを追加できます。

バインディングを自分で指定する `touying-reducer` もあります。

## シンプルなアニメーション

例：

```example
#import "@preview/touying:0.8.0": *
#import themes.metropolis: *
#import "@preview/cetz:0.5.2"
#import "@preview/fletcher:0.5.8" as fletcher: node, edge

// Touying向けCeTZ・Fletcherのバインディング
#let cetz-canvas = touying-reduce.with(cetz) // 名前を公開するパッケージ向けの新しい構文
#let fletcher-diagram = touying-reduce.with(fletcher)

#show: metropolis-theme.with(aspect-ratio: "16-9")

// CeTZのアニメーション
#slide[
  Cetz in Touying:

  #cetz-canvas({
    import cetz.draw: *
    
    rect((0,0), (5,5))

    (pause,)

    rect((0,0), (1,1))
    rect((1,1), (2,2))
    rect((2,2), (3,3))

    (pause,)

    line((0,0), (2.5, 2.5), name: "line")
  })
]

// Fletcherのアニメーション
#slide[
  Fletcher in Touying:

  #fletcher-diagram(
    node-stroke: .1em,
    node-fill: gradient.radial(blue.lighten(80%), blue, center: (30%, 20%), radius: 80%),
    spacing: 4em,
    edge((-1,0), "r", "-|>", `open(path)`, label-pos: 0, label-side: center),
    node((0,0), `reading`, radius: 2em),
    edge((0,0), (0,0), `read()`, "--|>", bend: 130deg),
    pause,
    edge(`read()`, "-|>"),
    node((1,0), `eof`, radius: 2em),
    pause,
    edge(`close()`, "-|>"),
    node((2,0), `closed`, radius: 2em, extrude: (-2.5, 0)),
    edge((0,0), (2,0), `close()`, "-|>", bend: -40deg),
  )
]
```


## onlyとuncover

同様に `only` と `uncover` も使えます。CeTZではTouyingのアニメーション命令を `(only(...),)` のような配列で囲む必要がありますが、Fletcherでは直接記述できます。

```example
#import "@preview/touying:0.8.0": *
#import "@preview/cetz:0.5.2"
#import themes.simple: *
#show: simple-theme.with(aspect-ratio: "16-9")

// Touying向けCeTZのバインディング
#let cetz-canvas = touying-diagram.with(cetz) // 名前を公開するパッケージ向けの新しい構文

== Only and Uncover in Cetz

Cetz in Touying in subslide #touying-get-config("subslide")

#cetz-canvas({
  import cetz.draw: *
  
  rect((0,0), (5,5))

  (uncover("2-3", {
    rect((0,0), (1,1))
    rect((1,1), (2,2))
    rect((2,2), (3,3))
  }),)

  (only(3, line((0,0), (2.5, 2.5), name: "line")),)
})

```

## onlyとuncover with slide self

スライドの `self` を渡してユーティリティメソッドを使用する方法もあります。その場合は配列で囲む必要はありません。ただし、サブスライドの数に合った `repeat` を指定する必要があります。


```example
#import "@preview/touying:0.8.0": *
#import "@preview/cetz:0.5.2"
#import themes.simple: *
#show: simple-theme.with(aspect-ratio: "16-9")

#slide(repeat: 3, self => [
  #let (uncover, only, alternatives) = utils.methods(self)

  Cetz in Touying in subslide #self.subslide:

  #cetz.canvas({
    import cetz.draw: *
    let uncover = uncover.with(cover-fn: hide.with(bounds: true)) // カバーにはCeTZのhide関数を使用
    
    rect((0,0), (5,5))

    uncover("2-3", {
      rect((0,0), (1,1))
      rect((1,1), (2,2))
      rect((2,2), (3,3))
    })

    only(3, line((0,0), (2.5, 2.5), name: "line"))
  })
])
```

