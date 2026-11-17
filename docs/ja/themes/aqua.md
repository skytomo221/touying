---
sidebar_position: 5
---

# Aquaテーマ

このテーマは [@pride7](https://github.com/pride7) によって作成されました。Typstの描画機能を活用した美しい背景が特徴です。

## 初期化

次のコードで初期化できます。

```typst
#import "@preview/touying:0.8.0": *
#import themes.aqua: *

#show: aqua-theme.with(
  aspect-ratio: "16-9",
  config-info(
    title: [タイトル],
    subtitle: [サブタイトル],
    author: [著者],
    date: datetime.today(),
    institution: [所属],
  ),
)

#title-slide()

#outline-slide()
```

`aqua-theme` 関数は次のパラメーターを受け取ります。

- `aspect-ratio`：スライドの縦横比。`"16-9"` または `"4-3"` を指定でき、既定値は `"16-9"` です。
- `header`：ヘッダーに表示する内容。既定値は `self => utils.display-current-heading(depth: self.slide-level)` です。`self => self.info.title` のような関数を指定して変更できます。
- `footer`：フッター右側に表示する内容。既定値は `context utils.slide-counter.display()` です。

Aquaテーマは `#alert[..]` 関数も提供します。`#show strong: alert` と組み合わせてスライド内の文字を強調できます。

## 配色

Aquaテーマの既定の配色は次のとおりです。

```typst
config-colors(
  primary: rgb("#003F88"),
  primary-light: rgb("#2159A5"),
  primary-lightest: rgb("#F2F4F8"),
  neutral-lightest: rgb("#FFFFFF"),
)
```

`config-colors()` 関数を使って、好みやプレゼンテーションのブランドに合わせた配色に変更できます。


## スライド関数群

Aquaテーマは次の独自スライド関数を提供します。

```typst
#title-slide(config: (:), extra: none, ..args)
```

`title-slide` は `self.info` から表示内容を読み取ります。追加情報は `extra` パラメーターで指定できます。

---

```typst
#outline-slide(config: (:), leading: 50pt)
```

アウトライン用スライドを表示します。`leading` で項目間の間隔を制御できます。

アウトライン用スライドは `place(hide(heading(..)))` で不可視の見出しを配置し、ヘッダーと発表者ノートのパネルから `utils.display-current-heading` を使って見出しを取得できるようにします。

---

```typst
#slide(
  config: (:),
  repeat: auto,
  setting: body => body,
  composer: auto,
)[
  ...
]
```

ヘッダーとフッターを備えた通常のスライド関数です。ヘッダーはテーマの `header` パラメーターで決まり、既定では現在のスライドレベルの見出しを表示します。

---

```typst
#focus-slide[
  ...
]
```

聴衆の注意を引くためのスライドです。背景色には `self.colors.primary` を使います。

---

```typst
#new-section-slide(config: (:), level: 1, body)
```

指定した見出しで新しいセクションを開始します。`config-common(new-section-slide-fn: ..)` に登録済みなので、通常は `= Title` のような見出しで自動的に呼ばれます。

## 発表者ノート

Aquaは独自の `notes` 関数を `config-common(notes-fn: notes)` に登録しています。これにより、セカンドスクリーンやノート専用表示にもテーマの配色が適用されます。`touying-notes` を基盤としており、独自の実装で上書きできます。

```typst
#show: aqua-theme.with(
  config-common(notes-fn: my-notes),
)
```

詳しくは[発表者ノート](../tutorials/speaker-notes.md)を参照してください。


## 使用例

```example
#import "@preview/touying:0.8.0": *
#import themes.aqua: *

#show: aqua-theme.with(
  aspect-ratio: "16-9",
  config-info(
    title: [タイトル],
    subtitle: [サブタイトル],
    author: [著者],
    date: datetime.today(),
    institution: [所属],
  ),
)

#title-slide()

#outline-slide()

= セクション

== Slide タイトル

#lorem(40)

#focus-slide[
  背景に基本色を使う別の方法です。
]

== Summary

#slide(self => [
  #align(center + horizon)[
    #set text(size: 3em, weight: "bold", fill: self.colors.primary)
    THANKS FOR ALL
  ]
])
```
