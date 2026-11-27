---
sidebar_position: 7
---

# ウェイポイント

ウェイポイントを使うと、スライドのアニメーションタイムライン上の位置に名前を付け、ハードコードしたサブスライド番号の代わりにラベルで参照できます。これにより、アニメーションの保守が容易になります。ウェイポイントの前に一時停止や項目を挿入すると、後に続くすべての位置が自動的にずれます。もう自分でサブスライドを数える必要はありません。

## 基本的な使い方

`#waypoint(<label>)` を配置して名前付きの位置をマークし、`#uncover`、`#only`、`#effect` でそのラベルを使用します。

```typst
#slide[
  Base content.
  #waypoint(<step-a>)
  #uncover(<step-a>)[Uncovered from step-a.]
  #waypoint(<step-b>)
  #only(<step-b>)[Only during step-b.]
]
```

進行する各ウェイポイント（デフォルトの動作）は、新しいサブスライドを作成します。ここでは `<step-a>` がサブスライド2、`<step-b>` がサブスライド3で発動します。

## 暗黙のウェイポイント

新しいラベルを `#uncover`、`#only`、`#effect`、`#item-by-item` に直接渡すと、暗黙のウェイポイントが自動的に生成されます。別途 `#waypoint` を呼び出す必要はありません。

```typst
#slide[
  First content.
  #uncover(<reveal>)[Appears from here.]
  #only(<final>)[Only on the last step.]
]
```

暗黙のウェイポイントはラベルごとに1回だけ登録されます（最初の出現が優先されます）。そのため、同じラベルへの複数の参照は同じ位置を共有します。

## ウェイポイントを使用したitem-by-item

`#item-by-item` は `start` パラメーターとしてラベルを受け付けます。項目はそのウェイポイントの位置から表示され始めます。

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

これによって4つのサブスライドが生成されます。項目は2、3、4で表示され（暗黙の `<list>` ウェイポイントはサブスライド2へ進みます）、`<done>` はサブスライド5で発動します。 

> 注意：ウェイポイントは、新しいウェイポイントが後に続くまでのすべてのサブスライドを自身の範囲に取り込みます。

## 進行しないウェイポイント

デフォルトでは、ウェイポイントはサブスライドカウンターを進めます。明示的なウェイポイントに `advance: false` を指定すると、新しいサブスライドを作成せずに位置をマークできます。

```typst
#slide[
  #waypoint(<here>, advance: false)
  Content at the current position.
]
```

## 階層的なラベル

ウェイポイントのラベルは `:` を区切りとして階層を形成できます。例えば `<part:intro>` と `<part:main>` です。親ラベル `<part>` を参照すると、ラベルが `part:` で始まるすべてのウェイポイントが結合され、それらが占める範囲全体を得られます。

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

親ラベル自体を宣言する必要はありません。いずれかのウェイポイントがそれを接頭辞として使用した時点で存在します。これまでと同様に、ラベルは1枚のスライドの中でのみ一意である必要があり、文書全体で一意である必要はありません。

## ウェイポイントマーカー

より細かく制御するには、ウェイポイントマーカー（`wp-m`）を使ってウェイポイントの範囲の特定の部分を参照します。

| マーカー | 意味 |
|---|---|
| `from-wp(<label>)` | ウェイポイントの最初のサブスライドからスライドの末尾まで。 |
| `until-wp(<label>)` | ウェイポイントの最初のサブスライドまでの、そのサブスライドを除くすべてのサブスライド。 |
| `get-first(<label>)` | ウェイポイントの範囲の最初のサブスライド。 |
| `get-last(<label>)` | ウェイポイントの範囲の最後のサブスライド。 |
| `prev-wp(<label>)` | 指定したウェイポイントの直前のウェイポイント。 |
| `next-wp(<label>)` | 指定したウェイポイントの直後のウェイポイント。 |
| `not-wp(<label>)`  | ウェイポイントの範囲に含まれないすべてのサブスライド。 |

```typst
#slide[
  #waypoint(<mid>)
  #uncover(<mid>)[Visible during mid.]
  #waypoint(<end>)
  #uncover(from-wp(<mid>))[From mid onward.]
  #only(prev-wp(<end>))[Only before end starts.]
]
```

必要な動作を正確に指定するため、ウェイポイントマーカーを組み合わせることもできます。

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
前述したように、ウェイポイントは後に続くサブスライドの範囲を取り込み、後からウェイポイントを再利用して範囲全体を参照できます。
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

ウェイポイントの開始値を明示的に設定することもできます。サブスライドのインデックスとほかのウェイポイントの両方を指定できます。`start` は `#waypoint` 自体に指定します。`#uncover`、`#only` などは `start` パラメーターを受け取りません。これを設定すると、`advance` と内容内でのウェイポイントの位置の両方に優先します。

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

ここでは `<done>` がサブスライド4に固定されているため、「All done」はサブスライド5の補足よりも1ステップ早く表示されます。また、2列目の `<parallel>` は `<done>` と同じ位置から始まります。

## グローバルなリンクアンカー

ウェイポイントは、取り込んだ内容の末尾にもリンクアンカーを生成します。これは、そのウェイポイントの最後のサブスライドにも生成するということです。スライドに `<myslide>`、ウェイポイントに `<first-wp>` というラベルが付いている場合、`<myslide.first-wp>` で参照できます。スライドのラベルは、スライドに属する見出しのラベル（`== Title <myslide>`）か、`#slide[..]` 呼び出しの後のラベル（`#slide[..]<myslide>`）のいずれかで、スライドの最初のサブスライドのアンカーにもなります。スライドを再呼び出ししたコピーでは、これらに `<myslide-1>` と `<myslide-1.first-wp>` という名前が付けられ、次のコピーには `<myslide-2>`、以降も同様に名前が付けられます。ラベルは1つの文書に1回だけ出現すべきだからです。


## その他の例

コールバック形式のスライド、CeTZやFletcherとの連携、`recall-subslide`、エッジケースなど、ウェイポイントのすべての機能を網羅した一連の例については、[`examples/waypoints.typ`](https://github.com/touying-typ/touying/blob/main/examples/waypoints.typ)を参照してください。
