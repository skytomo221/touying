---
sidebar_position: 6
---

# 複数ファイルによる構成

Touyingは通常のTypst文書と同様の簡潔な構文と柔軟な設定を備え、リアルタイムの差分コンパイルも維持しているため、大規模なスライド作成に適しています。

数十〜数百ページに及ぶ講義資料など、大量のスライドを作る場合には複数ファイルに分ける構成が便利です。

## 設定と内容の分離

基本的な構成は共通設定の `globals.typ`、エントリーポイントの `main.typ`、本文の `content.typ` の3ファイルです。

これらを分離することで、`main.typ` と `content.typ` の両方から循環参照なしに `globals.typ` を読み込めます。

`globals.typ` に共通関数やテーマの初期化を記述できます。

```typst
// globals.typ
#import "@preview/touying:0.8.0": *
#import themes.university: *

// as well as some utility functions
```

`main.typ` は `globals.typ` をインポートしてshowルールを適用し、`#include` で `content.typ` を読み込みます。

```typst
// main.typ
#import "/globals.typ": *

#show: university-theme.with(
  aspect-ratio: "16-9",
  config-info(
    title: [タイトル],
    subtitle: [サブタイトル],
    author: [著者],
    date: datetime.today(),
    institution: [所属],
    contact: [contact\@mail.com],
    logo: emoji.school,
  ),
)

#include "content.typ"
```

`content.typ` には実際のスライド内容を記述します。

```typst
// content.typ
#import "/globals.typ": *

= The セクション

== Slide タイトル

こんにちは、Touying！

#focus-slide[
  Focus on me.
]
```

## 複数のセクション

複数のセクションに分けるには `sections` ディレクトリを作り、`content.typ` を移動します。たとえば、次のようにします。

```typst
// main.typ
#import "/globals.typ": *

#show: university-theme.with(
  aspect-ratio: "16-9",
  config-info(
    title: [タイトル],
    subtitle: [サブタイトル],
    author: [著者],
    date: datetime.today(),
    institution: [所属],
    contact: [contact\@mail.com],
    logo: emoji.school,
  ),
)

#include "sections/content.typ"
// #include "sections/another-section.typ"
```

And

```typst
// sections/content.typ
#import "/globals.typ": *

= The セクション

== Slide タイトル

こんにちは、Touying！

#focus-slide[
  Focus on me.
]
```

これで大規模なスライドを複数ファイルに分割する方法がわかりました。