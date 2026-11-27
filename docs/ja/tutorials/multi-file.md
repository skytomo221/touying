---
sidebar_position: 6
---

# 複数ファイルによる構成

Touyingは、通常のTypst文書と同じくらい簡潔な構文と、多数のカスタマイズ可能な設定オプションを備えています。それでもリアルタイムのインクリメンタルコンパイルの性能を維持しているため、大規模なスライドの記述に適しています。

数十ページから数百ページに及ぶ講義資料など、大量のスライドを記述する必要がある場合は、Touyingの複数ファイル構成も試すことができます。

## 設定と内容の分離

Touyingのシンプルな複数ファイル構成は、グローバル設定ファイル `globals.typ`、メインのエントリーファイル `main.typ`、実際の内容を格納するコンテンツファイル `content.typ` の3つのファイルで構成されます。

これら3つのファイルを分離するのは、`main.typ` と `content.typ` の両方から循環参照を引き起こさずに `globals.typ` をインポートできるようにするためです。

`globals.typ` は、グローバルなカスタム関数をいくつか格納したり、Touyingのテーマを初期化したりするために使用できます。

```typst
// globals.typ
#import "@preview/touying:0.8.0": *
#import themes.university: *

// いくつかのユーティリティ関数も
```

プロジェクトのメインエントリーポイントである `main.typ` は、`globals.typ` をインポートしてshowルールを適用し、`content.typ` を `#include` を使って取り込みます。

```typst
// main.typ
#import "/globals.typ": *

#show: university-theme.with(
  aspect-ratio: "16-9",
  config-info(
    title: [Title],
    subtitle: [Subtitle],
    author: [Authors],
    date: datetime.today(),
    institution: [Institution],
    contact: [contact\@mail.com],
    logo: emoji.school,
  ),
)

#include "content.typ"
```

`content.typ` は実際の内容を記述する場所です。

```typst
// content.typ
#import "/globals.typ": *

= The Section

== Slide Title

Hello, Touying!

#focus-slide[
  Focus on me.
]
```

## 複数のセクション

複数のセクションを実装することも簡単です。`sections` ディレクトリを作成し、`content.typ` ファイルを `sections.typ` ディレクトリへ移動するだけです。例えば、次のようにします。

```typst
// main.typ
#import "/globals.typ": *

#show: university-theme.with(
  aspect-ratio: "16-9",
  config-info(
    title: [Title],
    subtitle: [Subtitle],
    author: [Authors],
    date: datetime.today(),
    institution: [Institution],
    contact: [contact\@mail.com],
    logo: emoji.school,
  ),
)

#include "sections/content.typ"
// #include "sections/another-section.typ"
```

そして、

```typst
// sections/content.typ
#import "/globals.typ": *

= The Section

== Slide Title

Hello, Touying!

#focus-slide[
  Focus on me.
]
```

これで、Touyingを使って大規模なスライドのための複数ファイル構成を実現する方法を学びました。