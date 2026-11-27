---
sidebar_position: 6
---

# 配布資料モード

配布資料モードでは、アニメーションのすべてのサブスライドを論理的なスライド1枚につき1ページにまとめることで、印刷や配布が可能なバージョンのプレゼンテーションを簡単に作成できます。

## 配布資料モードの有効化

```typst
config-common(handout: true)
```

これをテーマの設定内に配置します。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(
  aspect-ratio: "16-9",
  config-common(handout: true),
)

= Title

== Animated Slide

First item.

#pause

Second item (won't generate a separate page in handout mode).

#pause

Third item.
```

デフォルトでは、配布資料モードは各スライドの**最後の**サブスライドのみを残します。

配布資料モードは `config-common(export-mode: "handout")` でも設定でき、これはさらにソースファイルに一切手を加えずに、コマンドラインから `--input export-mode=handout` として渡すことができます。[出力モード](../output-modes)を参照してください。

## 残すサブスライドの選択

`handout-subslides` を使用して、配布資料への出力に残す特定のサブスライド（またはサブスライドの集合）を選択できます。

```typst
// 最初のサブスライドだけを残す（「変更前」のスナップショットに便利）
config-common(handout: true, handout-subslides: 1)

// 最初と最後のサブスライドを残す
config-common(handout: true, handout-subslides: (1, -1))

// 文字列で表された範囲を残す（`only`/`uncover` と同じ構文）
config-common(handout: true, handout-subslides: "1-2")

// 各ウェイポイントの最後のサブスライドを残す
config-common(handout: true, handout-subslides: "waypoints")
```

`"waypoints"` は[ウェイポイント](./waypoints)ごとに1ページを残し、それぞれにそのウェイポイントの最終状態を表示します。そのため、配布資料で途中のステップを省きながらアニメーションの各段階を追うことができます。例えば `(1, "waypoints")` のように、配列の1要素にすることもできます。ウェイポイントのないスライドは最後のサブスライドだけを残します。

個々のウェイポイントを選択することもできます。`<intro>` のような単独のラベルは、その内容が完全に表示されるウェイポイントの*最後の*サブスライドを表すため、`handout-subslides: (<intro>, <result>)` はウェイポイントごとに1ページを残します。代わりに最初のサブスライドを指定するには `get-first(<intro>)` を使用します。範囲マーカーはその範囲に含まれるすべてのサブスライドを残します。`from-wp(<intro>)` はウェイポイント以降のスライドの残り、`until-wp(<intro>)` はそれより前のすべて、`not-wp(not-wp(<intro>))` はそのウェイポイント1つに属するサブスライドだけを残します。

## 配布資料専用のスライド

`<touying:handout>` ラベルを使用して、配布資料モードで**のみ**表示され、通常のプレゼンテーションでは非表示になるスライドを作成できます。

```typst
== Extra Notes for Handout <touying:handout>

This slide is included when `handout: true` but invisible otherwise.
```

## 特定のモード専用の内容

`#handout-only[..]`、`#presentation-only[..]`、`#slides-only[..]`、`#article-only[..]` は、ある内容をそれに適さないモードから除外します。これらについては[出力モード](../output-modes)で説明しています。

## 作業のヒント

一般的なワークフローでは、発表中は `handout: false`（デフォルト）のままにしておき、聴衆と共有するPDFを出力する際に `handout: true` に切り替えます。

```typst
// 発表中
#show: my-theme.with(config-common(handout: false))

// 配布資料のPDFを作成するとき
#show: my-theme.with(config-common(handout: true))
```

ソースファイルに一切手を加えたくない場合は、上で述べたように、代わりにコマンドラインから `--input export-mode=handout` を使用してください。
