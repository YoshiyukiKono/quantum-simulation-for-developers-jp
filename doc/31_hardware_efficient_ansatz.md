以下は **フラットな Markdown** です。そのまま
`doc/31_hardware_efficient_ansatz.md` として保存できます。

---

# doc/31_hardware_efficient_ansatz.md

# 第31回 Hardware-efficient Ansatz ― 実機量子回路設計の基本パターン

## はじめに

前回は：

* shot数とは何か
* 測定誤差の構造
* Pauli grouping
* 分散最適化
* adaptive measurement
* classical shadow

を整理しました。

ここまでで：

実機量子計算に必要な

**測定戦略**

が理解できました。

今回は：

NISQアルゴリズム設計の中心となる

**Hardware-efficient Ansatz**

を扱います。

これは：

実機量子コンピュータで最も広く使われる回路設計

です。

---

# Ansatzとは何か？

Ansatzとは：

探索する状態の形を決める回路

です。

つまり：

```text
最適化対象となる量子状態の候補空間
```

を定義します。

例：

```text
|ψ(θ)⟩
```

ここで：

θ = パラメータ

です。

---

# なぜAnsatzが必要なのか？

理由：

量子状態の空間は巨大

だからです。

n量子ビットでは：

```text
2^n 次元
```

になります。

つまり：

すべて探索するのは不可能です。

そこで：

```text
探索範囲を制限する
```

必要があります。

それが：

Ansatz

です。

---

# Hardware-efficient Ansatzとは？

Hardware-efficient Ansatzとは：

量子ハードウェアで実装しやすい回路

です。

特徴：

```text
浅い回路
少ないCNOT
ネイティブゲート使用
```

です。

つまり：

ノイズに強い設計

になります。

---

# なぜHardware-efficientが重要なのか？

NISQでは：

回路深さが制限されます。

理由：

```text
深い回路
↓
デコヒーレンス
↓
計算失敗
```

です。

したがって：

浅い回路が必要になります。

---

# Hardware-efficient Ansatzの基本構造

典型構造：

```text
回転層
↓
エンタングル層
↓
回転層
↓
エンタングル層
```

これを：

繰り返します。

---

# 回転層とは何か？

回転層：

単一量子ビット回転

です。

例：

```text
RX
RY
RZ
```

---

# エンタングル層とは何か？

エンタングル層：

量子ビット同士を結合します。

例：

```text
CNOT
CZ
```

---

# 典型的な回路例

2量子ビットの場合：

```text
RY(θ₁) ──■──── RY(θ₃)
          │
RY(θ₂) ──X──── RY(θ₄)
```

これが：

最小のHardware-efficient Ansatz

です。

---

# なぜRYがよく使われるのか？

理由：

表現能力が高い

からです。

例：

```text
RXのみ → 不十分
RYのみ → 十分な場合が多い
```

さらに：

Z回転と組み合わせると：

任意状態に近づけます。

---

# 一般形のAnsatz

n量子ビットでは：

```text
layer = 1..L
```

として：

```text
[回転層 + エンタングル層] × L
```

となります。

ここで：

L = depth

です。

---

# depthとは何か？

depthとは：

層の数

です。

つまり：

```text
depth ↑
↓
表現能力 ↑
↓
ノイズ ↑
```

になります。

ここが：

設計のトレードオフ

です。

---

# Expressibilityとは何か？

Expressibilityとは：

表現できる状態の多様性

です。

つまり：

```text
大きい → 強いAnsatz
小さい → 弱いAnsatz
```

になります。

---

# なぜExpressibilityが重要なのか？

理由：

最適解が

Ansatzの中に存在しないと

見つけられないからです。

つまり：

```text
探索空間不足 → 解なし
```

になります。

---

# しかしExpressibilityを上げすぎると？

問題：

barren plateau

が発生します。

つまり：

```text
勾配が消える
```

現象です。

---

# Barren plateauとは何か？

最適化中：

```text
∂E/∂θ ≈ 0
```

になります。

つまり：

```text
更新できない
```

状態になります。

これは：

深いAnsatzほど起きやすいです。

---

# なぜ起きるのか？

理由：

状態がランダム化されるからです。

つまり：

```text
深い回路
↓
ランダム状態
↓
勾配消失
```

になります。

---

# Hardware-efficient Ansatzの利点

利点：

```text
実装が簡単
ノイズに強い
浅い回路
高速最適化
```

です。

---

# Hardware-efficient Ansatzの欠点

欠点：

```text
問題構造を使わない
最適解に届かない可能性
barren plateau発生
```

です。

---

# Problem-inspired Ansatzとの違い

比較：

| 種類                 | 特徴     |
| ------------------ | ------ |
| Hardware-efficient | 実装しやすい |
| Problem-inspired   | 精度が高い  |

つまり：

```text
実装性 vs 精度
```

の違いです。

---

# QAOAとの関係

QAOAも：

Problem-inspired Ansatz

の一種です。

つまり：

```text
問題構造を直接回路化
```

しています。

---

# VQEとの関係

VQEでは：

次のどちらかを使います：

```text
Hardware-efficient
Problem-inspired
```

用途で選択します。

---

# Qiskitでの実装例

例：

```python
from qiskit.circuit.library import EfficientSU2

ansatz = EfficientSU2(
    num_qubits=4,
    reps=2,
    entanglement="linear"
)

ansatz.draw("mpl")
```

これだけで：

Hardware-efficient Ansatz

が生成できます 🎯

---

# entanglementパターンとは何か？

代表例：

```text
linear
full
circular
```

です。

---

# linear entanglement

例：

```text
0 — 1 — 2 — 3
```

最も軽量です。

---

# full entanglement

例：

```text
全量子ビット接続
```

表現能力が高いですが：

ノイズが増えます。

---

# circular entanglement

例：

```text
0 — 1 — 2 — 3
|           |
+-----------+
```

中間的な選択です。

---

# 実機ではどれを選ぶべきか？

基本方針：

```text
まず linear
必要なら circular
最後に full
```

です。

---

# repsパラメータとは何か？

reps：

層の数

です。

例：

```python
reps = 1
reps = 2
reps = 3
```

増やすほど：

表現能力が上がります。

---

# 実機向け設計の基本戦略

推奨：

```text
浅く始める
↓
改善しなければ深くする
```

です。

---

# 初期値の重要性

重要：

初期パラメータが

最適化性能を左右します。

つまり：

```text
ランダム初期値
≠ 最良
```

です。

---

# Warm startとは何か？

Warm start：

古典解を初期値に使う方法

です。

例：

```text
古典最適化結果
↓
初期θに変換
```

です。

---

# Hardware-efficient Ansatzの設計指針まとめ

基本原則：

```text
浅くする
接続を減らす
回転を減らす
problem情報を少し入れる
```

です。

---

# ここまでで何ができるようになったか？

できるようになったこと：

* Ansatzの意味理解
* Hardware-efficient Ansatz理解
* depth設計理解
* expressibility理解
* barren plateau理解
* entanglement設計理解
* repsパラメータ理解

つまり：

実機向けAnsatz設計ができるようになりました 🚀

---

# 次回への接続

次回は：

**Problem-inspired Ansatz**

を扱います。

ここで：

問題構造を活用した量子回路設計

に進みます。

---

# 本章のまとめ

今回は：

* Ansatzとは何か
* Hardware-efficient Ansatzの構造
* expressibility
* barren plateau
* entanglement設計
* repsパラメータ
* Warm start

を整理しました。

次回は：

QAOAや化学系VQEで重要になる：

Problem-inspired Ansatz

を扱います。
