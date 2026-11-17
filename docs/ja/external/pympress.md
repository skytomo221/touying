---
sidebar_position: 4
---

# Pympress

[Pympress](https://github.com/Cimbali/pympress) は、発表や講演などのデュアルスクリーン環境向けに設計されたPDFプレゼンテーションツールです。多機能で柔軟に設定でき、持ち運びにも適しています。


## 発表者ノート

```typst
#import "@preview/touying:0.8.0": *
#import themes.university: *

#show: university-theme.with(
  aspect-ratio: "16-9",
  config-common(show-notes-on-second-screen: right),
)

= アニメーション

== シンプルなアニメーション

`#pause` を使うと、#pause 内容を後から表示できます。

#pause

Just like this.

#meanwhile

また、#pause `#meanwhile` を使うと、#pause 別の内容を同時に切り替えられます。

#speaker-note[
  + これは発表者ノートです。
  + `config-common(show-notes-on-second-screen: right)` を使わない限り表示されません。
]
```

![image](https://github.com/touying-typ/touying/assets/34951714/b43c7f99-c5f9-4084-aa70-c1561e8aafee)

生成したPDFはPympressで表示できます。

![image](https://github.com/touying-typ/touying/assets/34951714/afbe17cb-46d4-4507-90e8-959c53de95d5)
