---
sidebar_position: 1
---

# Touyingの紹介

[Touying](https://github.com/touying-typ/touying) は、[Typst](https://typst.app/) 向けの強力なスライド・プレゼンテーション用パッケージです。LaTeXのBeamerに似ていますが、Typstのモダンな構文と高速なコンパイルの恩恵を受けられます。このドキュメント全体を通して、スライドショー全体を **slides**、1ページを **slide**、アニメーションの1ステップによって生成されるサブページを **subslide** と呼びます。

## Touyingを使う理由

- **PowerPointとの比較** — Touyingは「内容とスタイルの分離」という考え方に従っています。軽量なマークアップを使ってプレーンテキストを書けば、テーマが視覚的なデザインを自動的に処理します。コードブロック、数式、定理環境を多用する研究中心のプレゼンテーションでは、特に生産性が高くなります。
- **Markdownベースのスライドとの比較** — Typstでは、Markdownベースのツールでは提供が難しい、きめ細かな組版の制御（ヘッダー、フッター、カスタムレイアウト、数式の第一級サポート）が可能です。Touyingはさらに `#pause` と `#meanwhile` を提供し、コードを中心とするワークフローに自然になじむ段階的なアニメーションを実現します。
- **Beamerとの比較** — Touyingは数秒（あるいは数十秒）ではなく、ミリ秒単位でコンパイルします。構文ははるかに簡潔で、テーマの作成や変更も容易です。Beamerとの機能面での同等性は高く、さらにCeTZ/Fletcherの図をアニメーション化する `touying-reduce`（==`touying-diagram`）などの追加機能も提供します。
- **Polyluxとの比較** — Touyingは `counter` と `locate` に依存せずに `#pause` を実装しているため、これらのプリミティブに伴う性能低下を回避できます。また、より豊富なテーマ用ユーティリティと、文書への変更を最小限に抑えてテーマを切り替えられる統一された設定APIも提供します。

## 名前の由来

「Touying」（投影、tóuyǐng）は中国語で「投影」を意味します。LaTeXの世界でドイツ語の *Beamer* が映写機を意味するのと同じです。

## スライドを作成する環境

主に2つの選択肢があります。

| 選択肢 | 説明 |
|--------|-------------|
| **[Typst Web App](https://typst.app/)** | ブラウザベースのエディターです。インストールは不要で、`typst.app` を開いて新しいプロジェクトを作成し、書き始めるだけです。リアルタイムプレビューと共同編集に対応しています。 |
| **[Tinymist LSP](https://github.com/Myriad-Dreamin/tinymist)** | LSP対応のエディターで動作する、多機能なTypst言語サーバーです。構文ハイライト、自動補完、エラー診断、スライドプレビューを提供し、VS Code拡張機能も利用できます。 |

どちらの方法でもTypstのパッケージレジストリからTouyingが自動的にダウンロードされるため、別途インストールする手順は必要ありません。

## Universe

[組み込みテーマ](https://touying-typ.github.io/themes/)には飽きましたか？

[Typst Universe](https://typst.app/universe/search/?q=touying) にはTouyingのユーザーがアップロードした多種多様なテーマがあります。ぜひ見てみてください。気に入るものがあるかもしれません。


## ギャラリー

Touyingは、コミュニティのメンバーがスライドを共有するための[ギャラリー](https://github.com/touying-typ/touying/wiki)を提供しています。ぜひご自身の作品も投稿してください！

## コントリビューション

Touyingは無料のオープンソースソフトウェアであり、コミュニティによって開発されています。[GitHub](https://github.com/touying-typ/touying) でIssueを作成したり、Pull Requestを送信したり、[touying-typ](https://github.com/touying-typ) 組織に参加したりできます。

## ライセンス

Touyingは [MITライセンス](https://github.com/touying-typ/touying/blob/main/LICENSE) で公開されています。