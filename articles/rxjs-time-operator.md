---
title: "RxJSの時間に関するoperatorまとめ"
emoji: "😽"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["rxjs"]
published: true
---

# 前提

Observable について学ばなければいけません。

## timer

- observable を作る
- 指定された期間で、数字を emit する

```typescript
//１秒後にemitされるobservableを作成
const source = timer(1000);
source.subscrive((val) => console.log(val));
```

```typescript
0;
```

## interval

- observable を作る
- 指定された時間単位で,連続的に数字を emit する

```typescript
//1秒ごとにemitされるobservableを作成
const source = interval(1000);
source.subscribe((val) => console.log(val));
```

```typescript
//unsubscribeするまで無限にemitされる
0,1,2,3,4,5,6.....
```

## auditTime

- 指定された期間の中で値を無視する。
- 期間で区切った時の古い値を出力する

```typescript
//１秒たつと出力さ荒れるobsservable
const source = interval(1000);
//5秒間まつ。最後にemitされた値を出力する
const example = source.pipe(auditTime(5000));
const subscribe = example.subscribe((val) => console.log(val));
```

```typescript
4...9...14
```

## debounceTime

- value の出力を指定された期間分遅らせる
- 期間で区切った時の、古い値を出力

```typescript
//１秒たつと出力さ荒れるobsservable
const source = interval(1000);
//5秒間まつ。最後にemitされた値を出力する
const example = source.pipe(debounceTime(5000));
const subscribe = example.subscribe((val) => console.log(val));
```

```typescript
//intervalで５秒たった後に、さらに5秒待って出力される
4...9...14
```

## throttleTime

- 指定された期間の中で値を無視する
- 期間の中で、新しい値を取り出す

```typescript
//１秒たつと出力さ荒れるobsservable
const source = interval(1000);
//5秒間まつ。最後にemitされた値を出力する
const example = source.pipe(throttleTime(5000));
const subscribe = example.subscribe((val) => console.log(val));
```

```typescript
 0...6...12
```

## bufferTime

- 指定された期間の中で emit を制御し、その間 emit される値を集めて配列で emit する

```typescript
//0.5秒に一回emitされるobservable
const source = interval(500);
//１秒までに出された値を配列で返すようにする
const subscription = source.pipe(bufferTime(1000));
subscription.subscribe((val) => console.log(val));
```

```typescript
[0,1], [2,3], [4,5]....
```

## delay

- 指定された時間分 emit されるタイミングを遅らせる

```typescript
const example = of(null);

//格observableにdelayをつけてタイミングを遅らせる
const message = merge(
	example.pipe(mapTo("Hello")),
	example.pipe(mapTo("World!"), delay(1000)),
	example.pipe(mapTo("Goodbye"), delay(2000)),
	example.pipe(mapTo("World!"), delay(3000))
);
const subscribe = message.subscribe((val) => console.log(val));
```

```typescript
 'Hello'...'World!'...'Goodbye'...'World!'
```

## timeout

- 指定された期間に emit されない場合に error をだす。

```typescript
//５秒後に値がemitされる
const time = time(5000);
//１秒で値が出されなかったらerrorをだす
const source = time.pipe(timeout(1000));
source.subscribe((catchError) => console.log("error"));
```

```typescript
"error";
```
