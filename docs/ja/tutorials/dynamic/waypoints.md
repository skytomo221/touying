---
sidebar_position: 7
---

# ウェイポイント

ウェイポイントを使うと、スライドのアニメーション上の位置に名前を付け、サブスライド番号を直接指定する代わりにラベルで参照できます。ウェイポイントより前に `pause` や項目を挿入しても、後続の位置が自動的に調整されるため、サブスライド数を手動で数える必要がなくなり、保守が容易になります。

## 基本的な使い方

`#waypoint(<label>)` で位置に名前を付け、`#uncover`、`#only`、`#effect` からそのラベルを使用します。

```typst
#slide[
  Base content.
  #waypoint(<step-a>)
  #uncover(<step-a>)[Uncovered from step-a.]
  #waypoint(<step-b>)
  #only(<step-b>)[Only during step-b.]
]
```

進行するウェイポイント（既定の動作）はそれぞれ新しいサブスライドを作成します。この例では `<step-a>` がサブスライド2、`<step-b>` がサブスライド3で有効になります。

## 暗黙のウェイポイント

`#uncover`、`#only`、`#effect`、`#item-by-item` に新しいラベルを直接渡すと、暗黙のウェイポイントが自動的に生成されます。別途 `#waypoint` を呼ぶ必要はありません。

```typst
#slide[
  First content.
  #uncover(<reveal>)[Appears from here.]
  #only(<final>)[Only on the last step.]
]
```

暗黙のウェイポイントはラベルごとに最初の出現時に一度だけ登録されるため、同じラベルを複数回参照しても位置は共通です。

## ウェイポイントを使用したitem-by-item

`#item-by-item` の `start` パラメーターにはラベルを指定できます。項目はそのウェイポイントの位置から順に表示されます。

```typst
#slide[
  #item-by-item(start: <list>)[
    - Alpha
    - Beta
    - Gamma
  ]
  #only(<done>)[All items revealed.]
]
```

この例では項目がサブスライド2、3、4で表示され、暗黙の `<list>` ウェイポイントがサブスライド2へ進み、`<done>` がサブスライド5で有効になります。

> 注意：ウェイポイントは次のウェイポイントが現れるまでのすべてのサブスライドを自分の範囲に含めます。

## 進行しないウェイポイント

ウェイポイントは既定でサブスライドカウンターを進めます。新しいサブスライドを作らず位置だけを記録するには、明示的なウェイポイントに `advance: false` を指定します。

```typst
#slide[
  #waypoint(<here>, advance: false)
  Content at the current position.
]
```

## 階層的なラベル

ウェイポイントのラベルは `:` を区切りとして階層化できます。たとえば `<part:intro>` と `<part:main>` です。親ラベル `<part>` を参照すると、`part:` で始まるウェイポイントをまとめ、それらが占める範囲全体を指定できます。

```typst
#slide[
  #waypoint(<part:intro>)
  Intro

  #waypoint(<part:main>)
  Main

  #uncover(<part>)[Shown from the first part: waypoint on.]

  #effect(text.with(fill: red), <part:main>)[Highlighted only during main.]
]
```

親ラベルを個別に宣言する必要はありません。接頭辞として使用された時点で存在します。ラベルの一意性は文書全体ではなく、スライド内でのみ必要です。

## ウェイポイントマーカー

ウェイポイントの範囲をより細かく指定するには、ウェイポイントマーカー（`wp-m`）を使用します。

| マーカー | 意味 |
|---|---|
| `from-wp(<label>)` | ウェイポイントの最初のサブスライドからスライド末尾まで。 |
| `until-wp(<label>)` | ウェイポイントの最初のサブスライドより前のすべて。 |
| `get-first(<label>)` | ウェイポイント範囲の最初のサブスライド。 |
| `get-last(<label>)` | ウェイポイント範囲の最後のサブスライド。 |
| `prev-wp(<label>)` | 指定したウェイポイントの1つ前。 |
| `next-wp(<label>)` | 指定したウェイポイントの1つ後。 |
| `not-wp(<label>)`  | ウェイポイントの範囲外のすべてのサブスライド。 |

```typst
#slide[
  #waypoint(<mid>)
  #uncover(<mid>)[Visible during mid.]
  #waypoint(<end>)
  #uncover(from-wp(<mid>))[From mid onward.]
  #only(prev-wp(<end>))[Only before end starts.]
]
```

ウェイポイントマーカーを組み合わせることで、より細かい動作を指定できます。

```typst
#slide[
  #waypoint(<mid>, advance:false)
  #uncover(<mid>)[Visible during mid.]
  #pause
  Second mid.
  #waypoint(<end>)
  End.

  #only(not-wp(get-first(<mid>)))[Soon finished.]
]
```


## 複雑な例
前述のように、ウェイポイントは後続のサブスライド範囲を取得し、その範囲全体を後から参照するために再利用できます。
```typst
#slide(composer: (1fr, 1fr))[
  #item-by-item(start: <steps>)[
    - Step one
    - Step two
    - Step three
  ]
  #pause
  Some remark.
  #uncover(<done>)[All done!]
][
  #alternatives(at: (<steps>, <done>))[
    _Working through the steps..._
  ][
    _Complete!_
  ]
]
```

## ウェイポイントの開始位置を明示する

ウェイポイントの開始位置はサブスライド番号や別のウェイポイントで明示できます。`start` は `#waypoint` 自体のパラメーターであり、`#uncover` や `#only` にはありません。指定すると、`advance` と本文中での位置よりも優先されます。

```typst
#slide(composer: (1fr, 1fr))[
  #item-by-item(start: <steps>)[
    - Step one
    - Step two
    - Step three
  ]
  #pause
  Some remark.
  #waypoint(<done>, start: 4)
  #uncover(from-wp(<done>))[All done, even before the remark!]
][
  #waypoint(<parallel>, start: <done>)
  Explaining stuff.
  #pause
  More explanation.
]
```

ここでは `<done>` がサブスライド4に固定されるため、「All done」はサブスライド5の注釈より1段階早く表示されます。2列目の `<parallel>` は `<done>` と同じ位置から始まります。

## グローバルなリンクアンカー

ウェイポイントは自身が対象とする内容の末尾、すなわちその最後のサブスライドにもリンクアンカーを作成します。スライドに `<myslide>`、ウェイポイントに `<first-wp>` というラベルがある場合、`<myslide.first-wp>` で参照できます。スライドのラベルは対応する見出し（`== Title <myslide>`）または `#slide[..]` 呼び出しの直後（`#slide[..]<myslide>`）に付けられ、スライド最初のサブスライドのアンカーにもなります。スライドを再呼び出しすると、ラベルが文書内で重複しないように、最初のコピーは `<myslide-1>` と `<myslide-1.first-wp>`、次は `<myslide-2>` のように名前が付けられます。


## その他の例

コールバック形式のスライド、CeTZ・Fletcherとの連携、`recall-subslide`、境界条件などウェイポイントの全機能を網羅する例は、[`examples/waypoints.typ`](https://github.com/touying-typ/touying/blob/main/examples/waypoints.typ)を参照してください。
