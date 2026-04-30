以下は **フラットな Markdown** です。そのまま
`doc/24_mixer_hamiltonian.md` として保存できます。

---

# doc/24_mixer_hamiltonian.md

# 第24回 Mixer Hamiltonianの設計 ― QAOA性能を決める核心要素

## はじめに

前回は：

* MaxCut問題とは何か
* Cost Hamiltonianの構築
* Pauli演算子による表現
* QAOAの基本実装
* 測定結果の読み取り
* repsパラメータの意味

を整理しました。

今回は：

QAOAの性能を左右する重要要素：

**Mixer Hamiltonian**

を扱います。

ここから：

QAOAの設計そのもの

に踏み込みます。

---

# QAOAの復習

QAOAは：

2種類の操作を交互に適用します：

```
Cost evolution
↓
Mixer evolution
↓
繰り返し
```

つまり：

探索と収束

を同時に実現するアルゴリズムです。

---

# Mixer Hamiltonianとは何か？

Mixer Hamiltonianとは：

探索空間を移動するための操作

です。

典型例：

```
H_M = Σ Xi
```

です。

これは：

ビット列の変化

を引き起こします。

---

# なぜMixerが必要なのか？

もしCost Hamiltonianだけなら：

状態は変化しません。

つまり：

```
探索ができない
```

という問題が発生します。

Mixer Hamiltonianは：

探索の自由度

を与えます。

---

# CostとMixerの役割の違い

比較：

| Hamiltonian | 役割   |
| ----------- | ---- |
| Cost        | 解を評価 |
| Mixer       | 解を探索 |

この2つのバランスが：

QAOAの性能

を決定します。

---

# 標準Mixerの意味

標準Mixer：

```
H_M = Σ Xi
```

は：

各量子ビットを反転可能にします。

つまり：

```
0 ↔ 1
```

の遷移が可能になります。

これは：

全探索空間への移動

を意味します。

---

# Mixer evolutionの数式構造

Mixer evolutionは：

```
U_M(β) = exp(-iβH_M)
```

です。

これにより：

状態が回転します。

---

# なぜXiを使うのか？

理由：

X演算子は：

ビット反転

を表すからです。

つまり：

```
|0⟩ → |1⟩
|1⟩ → |0⟩
```

になります。

---

# 探索空間のイメージ

Mixerなし：

```
1点に固定
```

Mixerあり：

```
状態空間を移動可能
```

になります。

---

# 制約付き最適化問題の課題

標準Mixerには：

問題があります。

それは：

制約を壊してしまう

ことです。

例：

```
頂点数固定
選択数固定
重み制約
```

などです。

---

# 制約付き問題とは何か？

例：

「ちょうど2つの頂点を選べ」

のような問題です。

この場合：

```
0000
1111
```

などは

許されません。

---

# なぜ標準Mixerでは不十分なのか？

標準Mixer：

```
Xi
```

は：

すべての遷移を許します。

つまり：

制約違反状態

にも移動してしまいます。

---

# 制約保存Mixerとは何か？

制約付き問題では：

制約を保存するMixer

が必要です。

例：

```
XY mixer
SWAP mixer
subspace mixer
```

などがあります。

---

# XY Mixerとは何か？

XY Mixer：

```
XiXj + YiYj
```

です。

これは：

```
01 ↔ 10
```

の変換のみを許します。

つまり：

粒子数保存

が実現できます。

---

# 粒子数保存の意味

例：

```
1001
```

という状態なら：

```
0110
```

には移動可能ですが：

```
1111
```

には移動できません。

つまり：

制約が守られます。

---

# XY Mixerの応用例

適用できる問題：

```
ポートフォリオ最適化
頂点選択問題
スケジューリング
資源割当問題
```

などです。

---

# SWAP Mixerとは何か？

SWAP Mixer：

```
SWAP(i, j)
```

を使います。

これは：

状態交換

を行います。

例：

```
1000 → 0100
```

です。

---

# Subspace Mixerとは何か？

Subspace Mixer：

制約を満たす部分空間

だけを探索します。

つまり：

```
許された状態のみ探索
```

になります。

---

# Mixer設計の基本戦略

Mixer設計の指針：

```
制約があるか？
↓
Yes → 制約保存Mixer
No  → 標準Mixer
```

です。

---

# Mixerが性能に与える影響

Mixerが変わると：

探索空間が変わります。

つまり：

```
収束速度
解の精度
最適解到達確率
```

が変わります。

---

# Mixer選択の直感

標準Mixer：

```
自由探索
```

制約Mixer：

```
構造化探索
```

になります。

---

# QAOA設計の核心

QAOA設計とは：

```
Cost Hamiltonian設計
+
Mixer Hamiltonian設計
```

です。

つまり：

Ansatz設計そのもの

です。

---

# QiskitでMixerを設計する考え方

Qiskitでは：

カスタムHamiltonian

としてMixerを定義できます。

例：

```python
from qiskit.quantum_info import SparsePauliOp

mixer = SparsePauliOp.from_list([
    ("XI", 1.0),
    ("IX", 1.0)
])
```

これを：

QAOAに渡します。

---

# 標準QAOAとの違い

通常：

```
Σ Xi
```

が自動使用されます。

カスタムMixerを使うと：

問題特化型QAOA

になります。

---

# 問題特化型QAOAとは何か？

Problem-informed Ansatz：

問題構造を回路に埋め込む

方法です。

これは：

UCCSDと同じ思想

です。

---

# VQEとの統一理解（再整理）

比較：

| Ansatz       | 特徴   |
| ------------ | ---- |
| EfficientSU2 | 汎用   |
| UCCSD        | 物理特化 |
| QAOA Mixer   | 問題特化 |

つまり：

Ansatz設計が性能を決めます。

---

# NISQ時代の設計思想

NISQでは：

```
深い回路 ❌
賢い回路 ⭕
```

が重要です。

Mixer設計は：

その代表例です。

---

# ここまでで何ができるようになったか？

できるようになったこと：

* Mixer Hamiltonian理解
* 標準Mixerの意味
* 制約保存Mixer理解
* XY Mixer理解
* Subspace探索理解
* Problem-informed Ansatz設計

つまり：

QAOA設計の核心

に到達しました 🚀

---

# 次回への接続

次回は：

**QAOAとVQEの統一理解**

を扱います。

ここで：

NISQアルゴリズムの共通構造

が見えてきます。

---

# 本章のまとめ

今回は：

* Mixer Hamiltonianとは何か
* 標準Mixerの役割
* 制約付き最適化問題
* XY Mixer
* SWAP Mixer
* Subspace Mixer
* 問題特化型Ansatz設計

を整理しました。

次回は：

VQEとQAOAを統一的に理解し

NISQアルゴリズム設計の全体像

を整理します。
