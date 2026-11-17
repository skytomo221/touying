---
sidebar_position: 6
---

# 配布資料モード

配布資料モードでは、アニメーションのサブスライドをスライドごとにまとめて、印刷や配布に適したプレゼンテーションを作成できます。

## 配布資料モードの有効化

```typst
config-common(handout: true)
```

テーマの設定内で次のように指定します。

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

既定では各スライドの**最後の**サブスライドだけが残ります。

`config-common(export-mode: "handout")` でも設定できます。ソースを変更せずに、コマンドラインから `--input export-mode=handout` を渡すこともできます。詳しくは[出力モード](../output-modes)を参照してください。

## 残すサブスライドの選択

`handout-subslides` で配布資料に残すサブスライドを個別に指定できます。

```typst
// Keep only the first subslide (useful for "before" snapshots)
config-common(handout: true, handout-subslides: 1)

// Keep the first and last subslides
config-common(handout: true, handout-subslides: (1, -1))

// Keep a range expressed as a string (same syntax as `only`/`uncover`)
config-common(handout: true, handout-subslides: "1-2")

// Keep the last subslide of every waypoint
config-common(handout: true, handout-subslides: "waypoints")
```

`"waypoints"` は各[ウェイポイント](./waypoints)の最終状態を1ページずつ残します。途中の段階を省きながら、アニメーションの区切りを配布資料に示せます。`(1, "waypoints")` のような配列にも指定できます。ウェイポイントのないスライドは最後のサブスライドだけを残します。

個々のウェイポイントも指定できます。`<intro>` のようなラベルは、そのウェイポイントの*最後の*サブスライドを表します。`handout-subslides: (<intro>, <result>)` ならそれぞれの最終状態を残します。最初のサブスライドを指定するには `get-first(<intro>)` を使います。範囲指定の `from-wp(<intro>)` はその地点以降、`until-wp(<intro>)` はその地点より前、`not-wp(not-wp(<intro>))` は該当ウェイポイント内だけを対象とします。

## 配布資料専用のスライド

`<touying:handout>` ラベルを使うと、通常の発表では非表示になり、配布資料モードで**のみ**表示されるスライドを作れます。

```typst
== Extra Notes for Handout <touying:handout>

This slide is included when `handout: true` but invisible otherwise.
```

## 特定のモード専用の内容

`#handout-only[..]`、`#presentation-only[..]`、`#slides-only[..]`、`#article-only[..]` は、対象外のモードから内容を除外する機能です。詳しくは[出力モード](../output-modes)を参照してください。

## 作業のヒント

通常は `handout: false`（既定値）で発表し、聴衆向けのPDFを出力するときに `handout: true` に切り替えます。

```typst
// During presentation
#show: my-theme.with(config-common(handout: false))

// When building the handout PDF
#show: my-theme.with(config-common(handout: true))
```

ソースファイルを変更したくない場合は、前述の `--input export-mode=handout` をコマンドラインから指定してください。
