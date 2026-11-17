---
sidebar_position: 5
---

# その他のアニメーション

Touyingは `touying-reduce`（別名 `touying-diagram`）を提供しており、CeTZやFletcherでアニメーションを直接利用できます。

## シンプルなアニメーション

例を見てください。

```example
#import "@preview/touying:0.8.0": *
#import themes.university: *
#import "@preview/cetz:0.5.2"
#import "@preview/fletcher:0.5.8" as fletcher: node, edge

// Touying向けCeTZ・Fletcherのバインディング
#let cetz-canvas = touying-reduce.with(cetz)
#let fletcher-diagram = touying-reduce.with(fletcher)

#show: university-theme.with(aspect-ratio: "16-9")

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


## `only`,`uncover` and `alternatives`

CeTZとFletcherでは `only`、`uncover`、`alternatives` も同様の構文で使えます。どちらも座標に基づく図なので見た目は同じですが、内部の処理は異なります。`only` は描画命令を除外し、`uncover` は描画を隠します。

```typst
// インポート、バインディング、テーマ

#slide[
  TouyingでのCeTZ：

  #cetz-canvas({
    import cetz.draw: *
    
    rect((0,0), (5,5))
    (pause,)

    rect((0,0), (1,1))

    (uncover(3, {
      rect((1,1), (2,2))
      rect((2,2), (3,3)) 
    }),)

    (only(3, line((0,0), (2.5, 2.5), name: "line") ),)
  })
]

#slide[
  TouyingでのFletcher：

  #fletcher-diagram(
    node-stroke: .1em,
    spacing: 4em,
    node((0, 0), [A], radius: 2em),
    pause,
    uncover("1-2", edge((0, 0), (1, 0), "-|>", stroke: blue)),
    uncover("2-", node((1, 0), [B], radius: 2em)),
    only(3, node((0, 1), [tmp], radius: 1em, fill: orange)),
  )
]
```
`effect` や `item-by-item` などの命令は、想定どおりに動作しない場合がある点に注意してください。

## コールバック形式のバインディング

CeTZで `(anim-cmd(), )` のような配列構文を書きたくない場合は、キャンバス内で `utils` を使い、必要な命令をローカルに再定義できます。CeTZが直接理解できる形式で出力できますが、サブスライド数は `repeat` で手動指定する必要があります。 

```example
#import "@preview/touying:0.8.0": *
#import "@preview/cetz:0.5.2"
#import themes.simple: *
#show: simple-theme.with(aspect-ratio: "16-9")

#slide(repeat: 3, self => [
  #let (uncover, only, alternatives) = utils.methods(self)

  TouyingのCeTZによるサブスライド #self.subslide:

  #cetz.canvas({
    import cetz.draw: *
    let uncover = uncover.with(cover-fn: hide.with(bounds: true))
    
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
（Fletcherでも利用できますが、通常はその必要はありません。）