---
title: "フロントエンドでDDDLikeなアーキテクチャを導入したときに困ったこと・対策（検討中も含む）"
emoji: "😸"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["DDD"]
published: true
---

# 前提のアーキテクチャ

<img width="650" alt="スクリーンショット 2019-12-09 21.18.32.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/220683/2daeb7de-0a87-368b-4ccd-5b00df6820d9.png">

### Presentation

表示を担う

### Repository

外部との接続を担う。

### Usecase(Application)

Repository 層、Store 層とのコネクション。
Repository 層からとってきたデータを Store に保存する。
ビジネスユースケースを記述。

### Entity

モデル。(DDD に置けるドメインモデリング)

### Query

Store 層から取り出したデータを編集して取り出す。

## 開発言語,フレームワーク

Typescript, Angular

# 困ったこと・対策

## Presenation 編

### Component していくが、再利用性がないものも Component 化していくのか迷った。

#### 例

- 「Google 検索」、「I'm Feeling Lucky」と書かれている Button は再利用性があるので、Component 化しよう。
- Google のロゴはコンポーネント化するのか迷う
  <img width="725" alt="スクリーンショット 2019-12-09 21.26.58.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/220683/8013811b-6804-df7a-b756-1a7fe48208f2.png">

#### 対策

一つの画面がもつ Component と再利用性がある Component に分けた。

```
├── modules
    ├── GoogleLogoComponent
└── pages
    ├── GoogleLogoComponent

```

> 参考
> https://tech.bitbank.cc/designsystem-theory-practice/

## Entity 編

### Entity、値オブジェクトを記述するとき、巷で流行っているような記述はフロントエンドではコスパが悪い、、？

```typescript
export class ValueObject {
	private _value: string;
	constructor(value: string) {
		this._value = value;
	}

	get getValue(): string {
		return this._value;
	}

	set setValue(value: string) {
		this._value = value;
	}
}
```

上記のように、一つのオブジェクトを表現するのにもこの記述がかかる。
そのくせ、基本的にフロントエンドでは**一つのドメインオブジェクトにおけるドメインロジックが少ないことが多い**（扱うもので異なる）

#### 対策

記述を interface にとどめた。Validation などの記述は同じファイルに function として記述。

```typescript
export interface ValueObject {
	value: string;
}

export function isExist(value: ValueObject): boolean {
	return this.value ? true : false;
}
```

### 値オブジェクトに関してはカプセルかする必要あるのか

例
UserId のような値オブジェクトは、store などのライブラリと相性が悪い。
(`userId: string | number`を想定してることが多い)

```typescript
export interface UserId {
	value: string;
}
```

#### 対策

uid のような値オブジェクトはライブラリとの相性の悪さを勘案して、type で string と同列な型とした。
これによって、引数を表す時などに、可視化はできるようになった。

```typescript
export type UserId = string;
```

## Query

### query 処理が煩雑になりがち。

ただ api から fetch した JsonData を使って query を処理するのは難しくなりがち。

### 対策

正規化することで、RDB のように query 処理を記述することができる。

> 参考
> https://www.slideshare.net/ayatas0623/reduxstate-129830690

## Store

### store の分割単位は Page 単位？ドメインオブジェクト単位？？？

一つの page では、複数のドメインオブジェクトを扱うことが多い。したがって、ドメインオブジェクト単位での Store の設計だと、扱う Store も多い。対して Page 単位で Store を構成することで、Store の扱いが Page のなかの限定的になるが、扱いやすくなる。

### 対策

現状ドメインオブジェクト単位にしています。ただ、これといった明確な理由はないのでベストプラクティスを模索中です、。

> 参考
> https://qiita.com/oi5u/items/4e2c5bc1d546be1b1c5d

## Usecase

### データ取得するのはどこの責務？

データ取得する場所は使っているフレームワーク、ライブラリなどで結構別れたりする。例えば Vue.js の場合は Vuex を Store で使ったりすることが多いが、Vuex のアクションでは非同期処理が勧められている。

> 参考
> https://vuex.vuejs.org/ja/guide/actions.html

つまり、Presentation で発火し、Action を Dispatch。そこで非同期で API を発火させ、ミューテーションに値を送る形になっている。ただ、ライブラリで勧められているならまだしも、Store で Repository を扱うのはどうかと疑問に思った。

### 対策

InitializeUsecase として、Usecase 層で処理を行う。

```typescript
export class InitializeUsecase {
	constructor() {}

	exec() {
		const repository = new EntityRepository();
		const entity = repository.fetch();
		const store = new Store();
		store.dispatch(entity);
	}
}
```

# 最後に

自分がフロントエンド DDD をする際に困ったところをかいつまんでまとめました。誰かの参考になりますと幸いです。
