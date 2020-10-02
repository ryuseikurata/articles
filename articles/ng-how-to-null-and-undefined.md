---
title: Angularにおけるnullとundefinedの使い分け
emoji: "🧠"
type: "tech"
topics: ["Angular", "typescript"]
published: true
---

# 課題

Javascript において、null と undefined はど扱っていけばいいのか。ということは考えていきたい。

null と undefined の詳細な違いは[こちら](https://qiita.com/shuhei/items/199281e07925d24059d5#:~:text=null%20%E3%81%AF%E4%BD%95%E3%81%8B%E3%82%92,%E3%81%AA%E3%81%A9%E3%81%AF%E5%85%A8%E3%81%A6%20undefined%20%E3%81%A7%E3%81%99%E3%80%82)を見ればわかるだろう。

# 注目ポイント

Angular は、web アプリケーションで、結局は view として表現していくために存在するもの。null と undefined もその観点において違いを書いてみる。

# undefined の使い方

undefined は**何もない**状態である。この何もないというのは、実行時に値がないということだけではなく、**永続機関から取得するものがない**ということである。Angular でおなじみの、AngularFirestore を使って例を出す。

## コード例と説明

```typescript
getByID$(account_id: string) {
    return this.firestore.collection<Account>('accounts').doc<Account>(account_id).valueChanges();
  }
```

上記の valueChanges()で返される型は、`Observable<Account | undefined>`である。undefined は、angular firestore の data を検索したときに、データベースに存在しなかったときに返される。このように、永続期間にデータとして存在しない時に undefined を使うという事をしたほうがよい。

# null の使い方

undefined は**何もない**状態である。この何もないというのは、実行時に値がないということだけではなく、**永続機関からまだ値が返ってこない**ということである。わかりやすくいうと、Loading 状態である。Angular でおなじみの、AngularFirestore を使って例を出す。

## コード例と説明

```typescript
const account$ =
    return this.firestore.collection<Account>('accounts').doc<Account>(this.account_id).valueChanges();
  }
```

```html
<component [account]="account$ | async"></component>
```

typescript 上で定義された Observable を async pipe(詳しくは[こちら](https://qiita.com/ryuseikurata/items/43d16cdbe697880ee55b))を通して component に値を流すことで、初期値は null 状態になる(詳しくは[こちら](https://blog.lacolaco.net/2020/02/async-pipe-initial-null-problem/))。つまり、component の account は、`Observable<Account> | undefined | null>`となる。初期値が null になることで、Loading 状態を作り出す事ができるのである。

# 使い分けのメリット

Component の状態を細かく出し分けする事ができる。現代のアプリケーションでは、UX が重要項目の一つにあり、一つの UI でも、UIStack(詳しくは[こちら](https://note.com/nowim/n/n185d63cfda5c))を定義していかねばならない（はず）。非同期データを扱う UI になると、**データがない状態**、**Loading 中**である Stack を表現することは必須となる。上記の使い分けをすることで、それぞれの Stack の UI を**一つの Component**に閉じ込める事ができ、非常に保守性の高いアプリケーション開発を行う事ができるのである。

## 例

### 値を取得

```typescript
const account$ =
    return this.firestore.collection<Account>('accounts').doc<Account>(this.account_id).valueChanges();
  }
```

### 親 Component

```html
<component [account]="account$ | async"></component>
```

### 子 Component

```html
<ng-container *ngIf="account; else noValue">
	<account></account>
</ng-container>
<ng-template #noValue>
	<!---永続期間にデータが存在しなかった時に表示-->
	<ng-container *ngIf="account === undefined; else loading">
		<not-found></not-found>
	</ng-container>
	<!---Loading中の時に表示--->
	<ng-template #loading>
		<loading></loading>
		<ng-template> </ng-template></ng-template
></ng-template>
```

# まとめ

以上は私が実際にアプリケーションを開発をしてきた上で整理された事項である。また、必ずしも正しくはなく、null と undefined の使い分けも千差万別かと思われる。何かしらの参考になれば幸いである。

# 参考リンク

- [null と undefined の違い](https://qiita.com/shuhei/items/199281e07925d24059d5#:~:text=null%20%E3%81%AF%E4%BD%95%E3%81%8B%E3%82%92,%E3%81%AA%E3%81%A9%E3%81%AF%E5%85%A8%E3%81%A6%20undefined%20%E3%81%A7%E3%81%99%E3%80%82/)
- [初期値 null 問題](https://blog.lacolaco.net/2020/02/async-pipe-initial-null-problem/)
- [UIStack とは](https://note.com/nowim/n/n185d63cfda5c)
