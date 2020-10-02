---
title: "RxJS mapとmergeMapの違い"
emoji: "📑"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["rxjs"]
published: true
---

# 前提

rxjs について、まだ何もわからない方は、こちらの記事を一度読んでいただけたらと思います。
https://tech.recruit-mp.co.jp/front-end/rxjs-intro/
と同時にこの記事では、

- ストリーム
- メッセージ
- pipe
- subscribe

について少し解説します。
特にストリーム、メッセージは、map と mergeMap の説明でよく使います。

## ストリーム

observable のことを指します。
`const source = of(1,2)`
上記は、of によって、変数が source が observable になっているので、変数 source はストリームといえます。

## メッセージ

ストリームの中に存在する値を指します。
`const source = of(1,2)`
of の引数に、1 と 2 を入れていることから、ストリームである source は、メッセージ 1 と 2 をもつといえます。

## pipe について

**オペレータ**を呼び出すためのメソッドです。
オペレータは、ストリームを操作します。
今回の主題である、map,mergeMap もオペレーターの区分に該当します。

```typescript
//ストリームを作る
const number = of(1,2)
//numberのメッセージで、オペレータを使うことができる。
const map = number.pipe(
    map((x) => x ** 2))
//subscribeで値を購読
map.subscribe( val => console.log(val);
```

```typescript
1;
4;
```

##結局 pipe と subscribe の違いは？

- pipe がオペレータを呼び出すためのメソッド
- subscribe がストリームを購読するメソッドです。

# Map

メッセージを処理して、新たなストリームを作成します。

```typescript
//ストリーム作成
const source = of(1, 2);
//各メッセージに10を足します。
const example = source.pipe(map((val) => val + 10));

const subscribe = example.subscribe((val) => console.log(val));
```

```typescript
11;
12;
```

# MergeMap

複数のストリームを一つのストリームにまとめます。

```typescript
// ストリーム作成
const numbers = of(1, 2, 3);

//mergeMapの引数の中では、numbersストリームのメッセージごとに、新たなストリームを作成しています。
//つまり、ストリームの中で、複数のストリームができています。
//mergeMapによって、それを一つにまとめます。
const result = numbers.pipe(mergeMap((x) => of(x)));

result.subscribe((x) => console.log(x));
```

```typescript
1;
2;
3;
```

# まとめ

- map は、メッセージを処理して、新たなストリームを作成します。
- mergeMap は複数のストリームを一つのストリームにまとめます。

# 引用

http://reactivex.io/documentation/observable.html
https://t-cr.jp/article/3551640ea6a96e173
http://wilfrem.github.io/learn_rx/operators.html
http://reactivex.io/rxjs/file/es6/operator/mergeMap.js.html
