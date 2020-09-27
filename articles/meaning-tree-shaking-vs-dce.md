---
title: "TreeShakingとDeleteCodeEliminationの意味の違い"
emoji: "🔥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["javascript"]
published: true
---


# 背景
「scssはmixin,変数定義したら使用してないコードはtree-shakingされる」といったら、ある方に「この場合は,DeleteCodeEliminationという」と言及されて両者の違いがわからなかったので、TreeShakingとDeleteCodeEliminationの意味の違いをおさらいしようと思います。

# それぞれの定義
wikipediaより引用します。
## TreeShaking
> コンピューティングでは、ツリーシェイクは、Dart、JavaScript、またはTypeScriptなどのECMAScript方言で記述されたコードを、Webブラウザーによって読み込まれる単一のバンドルに最適化するときに適用されるデッドコード除去手法

## DeleteCodeElimination
> コンピュータ・プログラムの最適化などの目的で行われるプログラムの変形のひとつで、到達不能コードや冗長なコードなどといった「デッドコード」を削除する操作である


# まとめ
TreeShakingは、javascriptに限るDeleteCodeEliminationという意味合いでした。

つまり、包含関係として**DeleteCodeElimination > TreeShaking**となります。

勉強になりました！
