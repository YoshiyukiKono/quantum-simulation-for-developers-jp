以下は **フラットな Markdown** です。そのまま
`doc/34_vqa_optimizer_strategy.md` として保存できます。

---

# doc/34_vqa_optimizer_strategy.md

# 第34回 Variational Quantum Algorithmの最適化戦略 ― Optimizer選択の実践指針

## はじめに

前回は：

* Barren Plateauとは何か
* 勾配消失の原因
* depth依存性
* qubit数依存性
* Warm start
* layer-wise training
* local cost function
* ADAPT-VQEによる回避戦略

を整理しました。

今回は：

Variational Quantum Algorithm（VQA）

のもう一つの核心：

**古典Optimizer設計**

を扱います。

テーマ：

* なぜOptimizerが重要なのか
* 勾配ベース vs 勾配なし
* ノイズ環境での選択
* 実務向けOptimizer選定指針

です。

---

# なぜOptimizerが重要なのか？

VQAは：

量子回路

＋

古典最適化

で構成されます：

```text
量子回路
↓
期待値計算
↓
Optimizer更新
↓
再実行
```

つまり：

Optimizerが収束性能を決めます。

---

# VQAの基本構造

VQAは次のループです：

```text
θ 初期化
↓
回路実行
↓
期待値評価
↓
θ 更新
↓
収束判定
```

この：

θ 更新

を担当するのがOptimizerです。

---

# Optimizerの分類

代表的分類：

| 種類      | 例                |
| ------- | ---------------- |
| 勾配ベース   | Gradient Descent |
| 準ニュートン法 | L-BFGS           |
| 勾配なし    | COBYLA           |
| 確率的     | SPSA             |

です。

---

# なぜ量子最適化は難しいのか？

理由：

評価関数が：

```text
ノイズあり
確率的
非凸
高次元
```

だからです。

つまり：

通常の機械学習より難しい問題です。

---

# 勾配ベースOptimizerとは？

例：

```text
Gradient Descent
Adam
L-BFGS
```

更新式：

```text
θ ← θ − η ∇E
```

です。

---

# 勾配ベースの利点

利点：

```text
高速収束
少ない反復回数
理論解析可能
```

です。

---

# 勾配ベースの欠点

問題：

```text
ノイズに弱い
plateauに弱い
微分計算が必要
```

です。

---

# Parameter Shift Ruleとは何か？

量子回路では：

勾配を直接計算できません。

代わりに：

Parameter Shift Rule

を使います：

```text
∂E/∂θ =
E(θ + π/2) − E(θ − π/2)
───────────────
        2
```

つまり：

2回の回路実行で

勾配が求まります。

---

# 勾配計算のコスト

パラメータ数：

n

なら：

```text
2n 回路実行
```

が必要になります。

つまり：

コストが増えます。

---

# 勾配なしOptimizerとは？

代表例：

```text
COBYLA
Nelder-Mead
Powell
```

特徴：

```text
勾配不要
```

です。

---

# なぜ勾配なしOptimizerが重要なのか？

理由：

実機では：

ノイズがある

からです。

つまり：

```text
正確な勾配が測れない
```

問題があります。

---

# COBYLAとは何か？

COBYLA：

Constrained Optimization BY Linear Approximation

です。

特徴：

```text
制約付き最適化可能
ノイズ耐性あり
VQEでよく使われる
```

---

# SPSAとは何か？

SPSA：

Simultaneous Perturbation Stochastic Approximation

です。

特徴：

```text
2回の評価で勾配推定可能
```

つまり：

高次元でも効率的です。

---

# SPSAの直感

通常：

nパラメータなら：

```text
2n 回評価
```

必要ですが：

SPSAでは：

```text
2回
```

だけです。

---

# なぜSPSAが重要なのか？

理由：

実機では：

回路実行コストが高い

からです。

つまり：

```text
回路実行回数削減
```

が重要になります。

---

# L-BFGSとは何か？

L-BFGS：

準ニュートン法

です。

特徴：

```text
高速収束
少ない反復回数
```

ただし：

ノイズに弱いです。

---

# Adamは使えるのか？

Adamは：

機械学習で有名ですが：

VQAでは：

やや不安定です。

理由：

```text
勾配ノイズが大きい
```

ためです。

---

# Optimizer選択の基本戦略

基本方針：

```text
シミュレータ → L-BFGS
実機 → SPSA
中規模問題 → COBYLA
```

です。

---

# シミュレータの場合

シミュレータでは：

ノイズなし

です。

つまり：

```text
勾配ベースOptimizerが有利
```

になります。

---

# 実機の場合

実機では：

ノイズあり

です。

つまり：

```text
SPSAが最有力候補
```

になります。

---

# ハイブリッド戦略

実務では：

次を使います：

```text
初期探索 → COBYLA
収束段階 → L-BFGS
```

です。

---

# 学習率（learning rate）の重要性

更新式：

```text
θ ← θ − η ∇E
```

ここで：

η

が重要です。

---

# learning rateが大きすぎる場合

問題：

```text
発散
```

します。

---

# learning rateが小さすぎる場合

問題：

```text
収束が遅い
```

になります。

---

# learning rateスケジューリング

改善策：

```text
初期 → 大きく
後半 → 小さく
```

です。

---

# 初期値戦略とOptimizerの関係

重要：

```text
初期値が悪い
↓
Optimizerが失敗
```

になります。

つまり：

Optimizer単体では

解決できません。

---

# shot数とOptimizerの関係

重要：

shot数が少ないと：

```text
評価関数が不安定
```

になります。

つまり：

Optimizer性能が低下します。

---

# ノイズ環境での実務戦略

推奨：

```text
SPSA
+
Error mitigation
+
adaptive shot allocation
```

です。

---

# QiskitでのOptimizer例

COBYLA：

```python
from qiskit.algorithms.optimizers import COBYLA

optimizer = COBYLA(maxiter=200)
```

---

# SPSAの例

```python
from qiskit.algorithms.optimizers import SPSA

optimizer = SPSA(maxiter=300)
```

---

# L-BFGSの例

```python
from qiskit.algorithms.optimizers import L_BFGS_B

optimizer = L_BFGS_B(maxiter=100)
```

---

# Optimizer設計の実践フロー

基本手順：

```text
初期値設定
↓
COBYLA探索
↓
SPSA適用
↓
必要ならL-BFGS微調整
```

です。

---

# VQEにおける典型設定

例：

```text
ansatz depth = 小
optimizer = COBYLA
shots = 1000
```

から開始します。

---

# QAOAにおける典型設定

例：

```text
p = 1
optimizer = SPSA
shots = 2048
```

が標準です。

---

# ここまでで何ができるようになったか？

できるようになったこと：

* Optimizer分類理解
* 勾配ベース理解
* 勾配なしOptimizer理解
* SPSA理解
* COBYLA理解
* L-BFGS理解
* learning rate設計理解
* 実機向けOptimizer選択理解

つまり：

VQA最適化戦略が設計できるようになりました 🚀

---

# 次回への接続

次回は：

**Shot-frugalアルゴリズム設計**

を扱います。

ここで：

測定コスト最小化

という

NISQ時代の核心テーマ

に入ります。

---

# 本章のまとめ

今回は：

* VQAにおけるOptimizerの役割
* 勾配ベースOptimizer
* 勾配なしOptimizer
* SPSA
* COBYLA
* L-BFGS
* learning rate設計
* 実機向け最適化戦略

を整理しました。

次回は：

shot数削減を中心とした：

**Shot-efficient VQA設計**

に進みます。
