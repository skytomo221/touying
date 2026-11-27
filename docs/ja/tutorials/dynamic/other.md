---
sidebar_position: 5
---

# その他のアニメーション

Touyingは `touying-reduce`（==`touying-diagram`）も提供しており、CeTZとFletcherですべてのアニメーションがネイティブに動作するようにします。

## シンプルなアニメーション

以下はその例です。

```example
#import "@preview/touying:0.8.0": *
#import themes.university: *
#import "@preview/cetz:0.5.2"
#import "@preview/fletcher:0.5.8" as fletcher: node, edge

// Touying向けCeTZとFletcherのバインディング
#let cetz-canvas = touying-reduce.with(cetz)
#let fletcher-diagram = touying-reduce.with(fletcher)

#show: university-theme.with(aspect-ratio: "16-9")

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


## `only`、`uncover`、`alternatives`

実際には、CeTZとFletcherの内部でも同じ構文で `only`、`uncover`、さらには `alternatives` を使用できます。CeTZとFletcherは一般に位置に基づいているため、結果の図は同じになりますが、内部の動作は異なります。`only` は描画コマンドを取り除きますが、`uncover` はそれをカバーします。

```typst
//インポート、バインディング、テーマ

#slide[
  Cetz in Touying:

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
  Fletcher in Touying:

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
 `effect` や `item-by-item` などのコマンドが期待どおりに動作しない場合があることに注意してください。

## コールバック形式のバインディング

CeTZで配列構文 `(anim-cmd(), )` を記述しなければならないのを避けたい場合は、キャンバス内でutilsを介して必要なコマンドをローカルに再定義できます。こうすると、それらはCeTZがネイティブに理解する形式を出力します。ただし、その場合は `repeat` を使ってサブスライドを手動で数える必要があります！ 

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
（これはFletcherでも機能しますが、実際には使用する理由はないはずです。）