---
sidebar_position: 5
---

# Aquaテーマ

このテーマは [@pride7](https://github.com/pride7) によって作成されたもので、Typstの描画機能を使って作られた美しい背景が特徴です。

## 初期化

次のコードで初期化できます。

```typst
#import "@preview/touying:0.8.0": *
#import themes.aqua: *

#show: aqua-theme.with(
  aspect-ratio: "16-9",
  config-info(
    title: [Title],
    subtitle: [Subtitle],
    author: [Authors],
    date: datetime.today(),
    institution: [Institution],
  ),
)

#title-slide()

#outline-slide()
```

`aqua-theme` 関数は次のパラメーターを受け取ります。

- `aspect-ratio`：スライドのアスペクト比です。"16-9" または "4-3" を指定でき、デフォルトは "16-9" です。
- `header`：スライドのヘッダーに表示される内容です。デフォルトは `self => utils.display-current-heading(depth: self.slide-level)` です。`self => self.info.title` のような関数を指定して、ヘッダーの内容をカスタマイズすることもできます。
- `footer`：フッターの右側に表示される内容です。デフォルトは `context utils.slide-counter.display()` です。

さらにAquaテーマは `#alert[..]` 関数を提供します。`#show strong: alert` 構文と組み合わせて、スライド内のテキストを強調できます。

## 配色

Aquaテーマではデフォルトで次の配色を使用します。

```typst
config-colors(
  primary: rgb("#003F88"),
  primary-light: rgb("#2159A5"),
  primary-lightest: rgb("#F2F4F8"),
  neutral-lightest: rgb("#FFFFFF"),
)
```

`config-colors()` 関数を使用して、好みやプレゼンテーションのブランドに合わせてこの配色を変更できます。


## スライド関数群

Aquaテーマは一連のカスタムスライド関数を提供します。

```typst
#title-slide(config: (:), extra: none, ..args)
```

`title-slide` は表示する情報を `self.info` から読み取ります。また、`extra` パラメーターを渡して追加情報を表示することもできます。

---

```typst
#outline-slide(config: (:), leading: 50pt)
```

アウトラインスライドを表示します。`leading` でアウトラインの項目間の間隔を制御します。

アウトラインスライドでは `place(hide(heading(..)))` を使って不可視の見出しを配置するため、スライドのヘッダーと発表者ノートのパネルの両方が `utils.display-current-heading` を通してタイトルを取得できます。

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

ヘッダーとフッターを備えた、デフォルトの通常スライド関数です。ヘッダーの内容はテーマの `header` パラメーターで決まり、デフォルトでは現在のスライドレベルの見出しを表示します。

---

```typst
#focus-slide[
  ...
]
```

聴衆の注意を引くために使用します。背景色は `self.colors.primary` です。

---

```typst
#new-section-slide(config: (:), level: 1, body)
```

指定されたタイトルで新しいセクションを開始します。すでに `config-common(new-section-slide-fn: ..)` で登録されているため、通常は `= Title` と記述することで呼び出され、手動で呼び出す必要はありません。

## 発表者ノート

Aquaは独自の `notes` 関数を定義し、`config-common(notes-fn: notes)` で登録しています。そのため、セカンドスクリーンとノート専用ビューのノートパネルにもテーマの配色が適用されます。この関数は `touying-notes` を基に構築されており、独自の実装で上書きできます。

```typst
#show: aqua-theme.with(
  config-common(notes-fn: my-notes),
)
```

詳細は[発表者ノート](../tutorials/speaker-notes.md)を参照してください。


## 使用例

```example
#import "@preview/touying:0.8.0": *
#import themes.aqua: *

#show: aqua-theme.with(
  aspect-ratio: "16-9",
  config-info(
    title: [Title],
    subtitle: [Subtitle],
    author: [Authors],
    date: datetime.today(),
    institution: [Institution],
  ),
)

#title-slide()

#outline-slide()

= The Section

== Slide Title

#lorem(40)

#focus-slide[
  Another variant with primary color in background...
]

== Summary

#slide(self => [
  #align(center + horizon)[
    #set text(size: 3em, weight: "bold", fill: self.colors.primary)
    THANKS FOR ALL
  ]
])
```
