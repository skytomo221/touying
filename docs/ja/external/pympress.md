---
sidebar_position: 4
---

# Pympress

[Pympress](https://github.com/Cimbali/pympress) は、プレゼンテーションや公開講演などのデュアルスクリーン環境向けに設計されたPDFプレゼンテーションツールです。設定の自由度が高く、機能が充実しており、移植性にも優れています。


## 発表者ノート

```typst
#import "@preview/touying:0.8.0": *
#import themes.university: *

#show: university-theme.with(
  aspect-ratio: "16-9",
  config-common(show-notes-on-second-screen: right),
)

= Animation

== Simple Animation

We can use `#pause` to #pause display something later.

#pause

Just like this.

#meanwhile

Meanwhile, #pause we can also use `#meanwhile` to #pause display other content synchronously.

#speaker-note[
  + This is a speaker note.
  + You won't see it unless you use `config-common(show-notes-on-second-screen: right)`
]
```

![image](https://github.com/touying-typ/touying/assets/34951714/b43c7f99-c5f9-4084-aa70-c1561e8aafee)

次に、Pympressを使用して表示できます。

![image](https://github.com/touying-typ/touying/assets/34951714/afbe17cb-46d4-4507-90e8-959c53de95d5)
