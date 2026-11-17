---
sidebar_position: 1
---

# Touyingの紹介

[Touying](https://github.com/touying-typ/touying) は、[Typst](https://typst.app/) でスライドやプレゼンテーションを作成するための高機能なパッケージです。LaTeXのBeamerに似ていますが、Typstの現代的な構文と高速なコンパイルを活用できます。このドキュメントでは、プレゼンテーション全体を **slides**、1ページを **slide**、アニメーションの各段階で生成されるページを **subslide** と呼びます。

## Touyingを使う理由

- **PowerPointとの比較** — Touyingは「内容とスタイルの分離」を重視します。軽量なマークアップを使ってテキストを書けば、テーマが見た目を自動的に整えます。コードブロック、数式、定理環境を多用する研究発表で特に効果的です。
- **Markdownベースのスライドとの比較** — Typstでは、ヘッダー、フッター、独自のレイアウト、数式などを細かく制御できます。Touyingはさらに `#pause` や `#meanwhile` を提供し、コード中心の作業方法に自然になじむ段階的なアニメーションを実現します。
- **Beamerとの比較** — Touyingのコンパイルは数秒〜数十秒ではなく、ミリ秒単位で完了します。構文はより簡潔で、テーマの作成や変更も容易です。Beamerの多くの機能に対応し、CeTZ/Fletcherの図をアニメーション化する `touying-reduce`（別名 `touying-diagram`）などの独自機能もあります。
- **Polyluxとの比較** — Touyingは `#pause` の実装に `counter` や `locate` を使わないため、それらに伴う性能低下を回避できます。テーマ用ユーティリティも充実しており、統一された設定APIによって文書をほとんど変更せずにテーマを切り替えられます。

## 名前の由来

「Touying」（投影、tóuyǐng）は中国語で「投影」を意味します。LaTeXの*Beamer*がドイツ語の「映写機」に由来するのと同様です。

## スライドを作成する環境

主に次の2つの方法があります。

| 選択肢 | 説明 |
|--------|-------------|
| **[Typst Web App](https://typst.app/)** | ブラウザ上で使えるエディターです。インストールは不要で、`typst.app` から新しいプロジェクトを作成するだけで始められます。リアルタイムプレビューと共同編集に対応しています。 |
| **[Tinymist LSP](https://github.com/Myriad-Dreamin/tinymist)** | LSP対応エディターで利用できるTypstの言語サーバーです。構文ハイライト、補完、エラー診断、スライドプレビューを提供します。VS Code拡張機能もあります。 |

どちらもTypstのパッケージレジストリからTouyingを自動的に取得するため、Touying自体を別途インストールする必要はありません。

## Universe

[組み込みテーマ](https://touying-typ.github.io/themes/)以外も試してみたいですか？

[Typst Universe](https://typst.app/universe/search/?q=touying) には、Touyingのユーザーが公開した多彩なテーマがあります。ぜひ探してみてください。


## ギャラリー

Touyingの[ギャラリー](https://github.com/touying-typ/touying/wiki)では、コミュニティのメンバーがスライドを共有しています。ぜひ自分の作品も投稿してください。

## コントリビューション

Touyingは無料のオープンソースソフトウェアで、コミュニティによって開発されています。[GitHub](https://github.com/touying-typ/touying) ではIssueやPull Requestを通じて貢献したり、[touying-typ](https://github.com/touying-typ) 組織に参加したりできます。

## ライセンス

Touyingは [MITライセンス](https://github.com/touying-typ/touying/blob/main/LICENSE)で公開されています。