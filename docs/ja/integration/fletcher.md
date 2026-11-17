---
sidebar_position: 4
---

# Fletcher

Touyingは同義の関数 `touying-diagram` と `touying-reduce` を提供しており、Fletcherの図に `pause` や `meanwhile` などのアニメーションを追加できます。

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
  TouyingでのCeTZ：

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
  TouyingでのFletcher：

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

コールバック形式の例：

```example
#import "@preview/touying:0.8.0": *
#import "@preview/fletcher:0.5.8" as fletcher: diagram, edge, node
#show: themes.simple.simple-theme.with(aspect-ratio: "16-9")

#let diagram = touying-diagram.with(fletcher)

#slide(repeat: 6, self => {
  let (uncover, only, alternatives) = utils.methods(self)
  let uncover = uncover.with(cover-fn: fletcher.hide)
  diagram(
    node((0, 0), name: <A>)[$A$],
    pause,
    edge("->"),
    node((1, 0), name: <B>)[$B$],
    pause,
    edge("->"),
    node((2, 0), name: <C>)[$C$],
    uncover("4,6", edge(<A>, "~", <B>, bend: 40deg, stroke: red)),
    only("5,6", edge(<B>, "~", <C>, bend: 40deg, stroke: green)),
    only("6", edge(<C>, "~", <A>, bend: 40deg, stroke: blue)),
  )
})
```

バージョン0.7.0以降は、コールバック形式を使わずに同じことができます。