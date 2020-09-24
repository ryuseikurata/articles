---
title: "記憶のメカニズムをコーディングしてみた"
emoji: "🧠"
type: "tech"
topics: ["typescript", "neuroscience"]
published: true
---

# はじめに
日々の集中力をあげるために脳の仕組みを学びたいと思って、最近、[進化しすぎた脳―中高生と語る「大脳生理学」の最前線 (ブルーバックス)](https://amzn.to/2FRNeCA)という本を読みました。脳科学を学ぶ初心者入門のような本で、誰でも簡単にスラスラと読むことができます。目の錯覚の仕組みや、睡眠など、脳に関するあらゆるトピックをカバーしていま。読み進めると巻末に、行列計算で記憶の仕組みを再現するという内容がありまして、せっかくなので、typescriptでコード化してみました。([こちら](http://www.gaya.jp/spiking_neuron/matrix.htm)で著者が内容を公開しています。)

:::message
あくまでイメージなので、厳密な仕組みではないことをご了承ください。
:::

# ざっくりとした記憶のメカニズム

## 前提の用語解説
- 脳で情報処理を行っている細胞をニューロン(=神経細胞)と言う。
- ニューロン間の情報の受け渡しを行っている機関をシナプスと言う。

## 仕組みの解説

- シナプスの強化によって、情報の伝達効率が上がり、思い出しやすくなる。
- シナプスの強化は、そのシナプスにひもづくニューロン同士が同時に活動したことで行われる。
- ニューロンAと、ニューロンBが同時に活動したとき、シナプスの強度が上昇する。同時に活動したイベントを1,活動しなかったイベントを-1と表すと、(A,B,C)=(1,1,-1)(以下パターンAと呼ぶ)であったとき、ABのシナプスが強化され、AC、ABのシナプスが弱化する。シナプスの強化を1、弱化を-1と表すとすると、以下のように表せる。

### 行列

![](https://storage.googleapis.com/zenn-user-upload/xsbg8jjfkhz9tfqhhkv52u9wgtot)

### 初期値
![](https://storage.googleapis.com/zenn-user-upload/4vu0l6dltbjqcb2yim1lk6g8ynob)

### パターンAを数値で起こした結果
![](https://storage.googleapis.com/zenn-user-upload/mz58zxpdvfekibe5znb0ppgrjs1r)



パターンAが、もう二回起きると、上記のシナプス強度は

![](https://storage.googleapis.com/zenn-user-upload/w7hbd4ow9olepf8hce49z3t1fg93)

思い出す時には、パターンAを以上の行列にかける。すると、

![](https://storage.googleapis.com/zenn-user-upload/mbqenijdpikdodf5zwt88mvzw9aj)

となりパターンAが出力される。これが**思い出した**と言うことになる。


- 以上の説明を実装する。
- 詳しい説明は[こちら](http://www.gaya.jp/spiking_neuron/matrix.htm)をチェック

# モデリング
- ニューロン同士の活動の状態（同時に活動したかしてないか)をNeuralActivityとすると ` type = NeuralActivity = 1 | 0 `
- これがニューロンそれぞれで行われるので、`export type NeuralActivityPattern = NeuralActivity[]`
- 上記の配列のlengthはニューロンの数に依存する。
- シナプスの強度を行列にしたものを`type SynapseMatrix = number[][]`とする


# 実装例
## ニューロンの活動パターンを脳に入力するメソッド

```typescript
input(pattern: NeuralActivityPattern) {
		pattern.forEach((act, actIndex) => {
			for (var _i = 0; _i < pattern.length; _i++) {
				if (actIndex !== _i) {
					this._synapse_matrix[actIndex][_i] += act;
				}
			}
		});
		return this._synapse_matrix;
	}
```

## 入力されたパターンが覚えているかを確認するメソッド

```typescript
hasRecalled(pattern: NeuralActivityPattern) {
		let _hasRecalled = false;
		const result = pattern.map((num, index) => {
			const innerd = this._synapse_matrix[0][index] * num;
			return innerd > 0 ? 1 : -1;
		});
		result.forEach((resultNum, index) => {
			if (pattern[index] !== resultNum) {
				_hasRecalled = false;
			} else {
				_hasRecalled = true;
			}
		});
		return _hasRecalled;
	}
```



# 感想
- この本のおかげで、脳もコンピュータはとても似通った性質を持っており、脳科学を学ぶことで、プログラミングのエッセンスを逆輸入できるのでは？(ディープラーニングもその一つ）と感じたので、もうちょっと脳の仕組みを調べたいと思いました。
- typescriptで行列計算は難しい。コードももっとうまい具合にできると思います。PR待ってます！(githubのRepositoryは[こちら](https://github.com/ryuseikurata/neural_network))
