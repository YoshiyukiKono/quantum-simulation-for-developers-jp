以下は **フラットな Markdown** です。そのまま
`doc/35_shot_frugal_vqa.md` として保存できます。

---

# doc/35_shot_frugal_vqa.md

# 第35回 Shot-frugal VQA設計 ― 測定コストを最小化する実践戦略

## はじめに

前回は：

* 勾配ベースOptimizer
* 勾配なしOptimizer
* COBYLA
* SPSA
* L-BFGS
* learning rate設計
* 実機向けOptimizer選択戦略

を整理しました。

ここまでで：

Variational Quantum Algorithm（VQA）

の最適化部分が理解できました。

しかし：

NISQ時代の最大のボトルネックは

**測定コスト（shot cost）**

です。

今回は：

shot-efficient（shot-frugal）VQA設計

を扱います。

---

# なぜshot数が重要なのか？

量子計算では：

期待値は直接計算できません。

代わりに：

統計推定します：

```text
多数回測定
↓
平均値計算
↓
期待値推定
```

つまり：

shot数が増えるほど

計算コストが増加します。

---

# 実機計算の本当のコスト構造

実際には：

回路実行より

測定のほうが支配的です：

```text
回路構築 < 測定回数
```

特に：

VQE

では顕著です。

---

# VQEにおけるshot数の問題

VQEでは：

Hamiltonianが

多数のPauli項に分解されます：

```text
H = Σ c_i P_i
```

すると：

```text
各P_iごとに測定必要
```

になります。

---

# shot数のスケーリング問題

典型例：

| qubit数 | Pauli項数 |
| ------ | ------- |
| 4      | 数十      |
| 8      | 数百      |
| 16     | 数千      |

つまり：

測定回数が爆発します。

---

# Shot-frugal設計とは何か？

Shot-frugal設計とは：

少ない測定回数で

高精度推定を行う戦略です。

つまり：

```text
情報量 / shot
```

を最大化します。

---

# 基本戦略① Pauli grouping

既に学んだ通り：

可換演算子は

同時測定できます：

```text
[P_i, P_j] = 0
```

なら：

同一basisで測定可能です。

---

# groupingの効果

例：

| 方法       | 測定回数 |
| -------- | ---- |
| naive    | 100  |
| grouping | 15   |

になります。

これは：

最も基本的な削減方法です。

---

# 基本戦略② importance sampling

重要な項ほど

多く測定します：

```text
|c_i| 大 → 多く測定
|c_i| 小 → 少なく測定
```

これで：

分散を最小化できます。

---

# 分散最小化の考え方

期待値誤差：

```text
error ∝ √Var / shots
```

つまり：

分散が大きい項に

shotを集中します。

---

# 基本戦略③ adaptive shot allocation

途中結果を見ながら：

shot配分を変更します：

```text
初期測定
↓
重要項特定
↓
再測定
```

です。

---

# なぜadaptiveが有効なのか？

理由：

全項を均等測定するのは

非効率だからです：

```text
均等測定 ❌
重要度測定 ⭕
```

になります。

---

# 基本戦略④ classical shadow

Classical shadowとは：

ランダム測定を使って

多数の期待値を同時推定する方法

です。

特徴：

```text
測定回数削減
```

が可能になります。

---

# classical shadowの直感

通常：

```text
N observables
→ N 回測定
```

ですが：

classical shadowでは：

```text
N observables
→ log(N) 回測定
```

になります。

---

# 基本戦略⑤ measurement reuse

同じ測定結果を：

複数の期待値推定に使います：

```text
1回測定
→ 複数observable推定
```

です。

---

# 基本戦略⑥ early stopping

収束途中で：

測定を止めます：

```text
十分な精度達成
↓
追加測定不要
```

です。

---

# shot数とOptimizerの関係

重要：

shot数が少なすぎると：

```text
評価関数がノイズ化
```

します。

すると：

Optimizerが誤動作します。

---

# 実践的shotスケジューリング

推奨：

```text
初期探索 → 少shot
収束段階 → 多shot
```

です。

---

# multi-stage shot strategy

例：

```text
stage1 → 128 shots
stage2 → 512 shots
stage3 → 2048 shots
```

段階的に増やします。

---

# coarse-to-fine最適化

戦略：

```text
粗探索
↓
詳細探索
```

です。

これは：

古典最適化でも使われます。

---

# stochastic objective optimization

shot数を減らすと：

評価関数が確率的になります：

```text
E(θ) ≈ noisy(E)
```

ですが：

SPSAなどは

これに強いです。

---

# SPSAとshot削減の相性

SPSAは：

少ない評価回数で

勾配推定できます：

```text
2回評価
```

のみです。

したがって：

shot-efficient設計と

非常に相性が良いです。

---

# Hamiltonian truncation

小さい係数の項を：

無視する方法です：

```text
|c_i| < ε → 無視
```

これで：

測定数削減できます。

---

# なぜ有効なのか？

理由：

寄与が小さいからです：

```text
影響 ≈ 小
```

となります。

---

# Ansatz設計との関係

浅いAnsatzほど：

shot-efficientです：

```text
depth ↓
↓
測定数 ↓
```

になります。

---

# grouping + adaptiveの組み合わせ

最強戦略：

```text
Pauli grouping
+
adaptive allocation
```

です。

これは：

現在の標準技術です。

---

# 実機向けshot設計テンプレート

推奨フロー：

```text
Pauli grouping
↓
importance sampling
↓
adaptive allocation
↓
classical shadow
```

です。

---

# Qiskitでのshot指定例

例：

```python
sampler.run(circuit, shots=1024)
```

これで：

shot数を制御できます。

---

# 実務での典型shot設計

例：

| フェーズ | shots |
| ---- | ----- |
| 初期探索 | 128   |
| 中間探索 | 512   |
| 最終収束 | 4096  |

です。

---

# Shot-frugal設計のまとめ原則

基本方針：

```text
均等測定しない
重要項を優先
段階的測定
結果を再利用
```

です。

---

# ここまでで何ができるようになったか？

できるようになったこと：

* shot数設計理解
* Pauli grouping活用
* importance sampling理解
* adaptive measurement理解
* classical shadow理解
* truncation戦略理解
* multi-stage測定理解

つまり：

実機向け測定最適化戦略

が設計できるようになりました 🚀

---

# 次回への接続

次回は：

**Noise-awareアルゴリズム設計**

を扱います。

ここで：

ノイズを前提とした量子回路設計

という

NISQアルゴリズムの完成形

に入ります。

---

# 本章のまとめ

今回は：

* shot-efficient設計とは何か
* Pauli grouping
* importance sampling
* adaptive shot allocation
* classical shadow
* truncation戦略
* multi-stage shot scheduling

を整理しました。

次回は：

実機計算の最終テーマ：

**Noise-aware VQA設計**

に進みます。
