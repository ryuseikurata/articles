---
title: "Angular の ngOninitの必要性 ~なぜconstructorではダメなのか~"
emoji: "🙌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Angular"]
published: true
---

# Angular とは

「Angular」は、Google によって開発されている JavaScript フレームワークです。
アプリケーションを作成する時、ページをクリックしたときに発火させたいイベントが多々あります。それを実現させたいとき、ngOninit が必要となります。(constructor ではできない）

# constructor とは

Typescript,Javascript に付随するものである
Class の初期化時に発火する

```typescript
class Hoge {
	id: number;
	constructor(num: number) {
		this.id = num;
	}
}
```

```typescript
let h = new Hoge(1)
console.log(h.id)
==> 1
```

# ngOnInit とは

Angular に付随するものである。
Lifecycle の一部
Lifecycle とは、ディレクティブやコンポーネントが変化するタイミングで、コールバックを設定できる仕組みである。

つまり、Angular の LifeCycle に乗っ取った処理をすることができるのである。
下記で使用しているのは、Angular の LifeCycle の一つの ngOnInit である。(Component の初期化時に発火する)

```typescript
import { Component, OnInit } from "@angular/core";

@Component({
	selector: "app-hoge",
	templateUrl: "./hoge.component.html",
	styleUrls: ["./hoge.component.scss"],
})
export class HogeComponent implements OnInit {
	constructor() {
		console.log("constructor");
	}

	ngOnInit() {
		console.log("ngOnInit");
	}
}
```

```typescript
===> 'constructor'
===> 'ngOnInit'
```

# まとめ

Angular では、constructor は、component 作成途中で実行されます。
ngOnInit は component 作成後に呼び出されます。
したがって、アプリケーションを作成する時、ページをクリックしたときにイベントを発火させたいというような UIUX 観点から見た要件は、ngOnInit(LyfeCycle)を使用することで簡単に実現することができるのです。

# 参考

http://blog.yuhiisk.com/archive/2016/05/02/angular2-lifecycle-hooks.html
