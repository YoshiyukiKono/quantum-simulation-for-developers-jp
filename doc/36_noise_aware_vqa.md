以下は **フラットな Markdown** です。そのまま
`doc/36_noise_aware_vqa.md` として保存できます。

---

# doc/36_noise_aware_vqa.md

# 第36回 Noise-aware VQA設計 ― ノイズを前提にアルゴリズムを設計する

## はじめに

前回は：

* shot-efficient設計
* Pauli grouping
* importance sampling
* adaptive shot allocation
* classical shadow
* Hamiltonian truncation
* multi-stage shot scheduling

を整理しました。

ここまでで：

測定コスト最適化

が理解できました。

今回は：

NISQアルゴリズム設計の最終段階：

**Noise-aware VQA設計**

を扱います。

これは：

ノイズを除去するのではなく

**ノイズを前提として設計する**

という発想です。

---

# なぜNoise-aware設計が必要なのか？

理想：

```text
noise = 0
```

現実：

```text
noise > 0
```

です。

つまり：

```text
ノイズなし設計 ❌
ノイズ前提設計 ⭕
```

が必要になります。

---

# Noise-aware設計とは何か？

Noise-aware設計とは：

次を最適化することです：

```text
アルゴリズム性能 − ノイズ影響
```

つまり：

```text
精度最大化
ではなく
実機性能最大化
```

です。

---

# NISQアルゴリズムの設計方針

基本戦略：

```text
回路を短くする
測定を減らす
エンタングルメントを制御する
誤差を補正する
```

です。

---

# ノイズの種類の再整理

代表例：

| ノイズ           | 内容      |
| ------------- | ------- |
| Gate error    | ゲート誤差   |
| Readout error | 測定誤差    |
| Decoherence   | 状態崩壊    |
| Crosstalk     | 量子ビット干渉 |

それぞれ：

対策が異なります。

---

# Noise-aware Ansatz設計

重要原則：

```text
浅い回路
局所接続
少ないCNOT
```

です。

理由：

CNOTが最もノイズに弱いからです。

---

# なぜCNOTが問題なのか？

単一量子ビットゲート：

```text
誤差 ≈ 小
```

二量子ビットゲート：

```text
誤差 ≈ 大
```

つまり：

```text
CNOT削減 = 精度向上
```

になります。

---

# Hardware topologyを考慮する

量子ハードウェアでは：

接続制約があります：

```text
0 — 1 — 2 — 3
```

などです。

遠距離接続には：

SWAPが必要になります。

---

# SWAPの問題

SWAPは：

実際には：

```text
3 CNOT
```

です。

つまり：

ノイズが増えます。

---

# topology-aware設計とは？

例：

```text
隣接qubitのみ使用
```

です。

つまり：

```text
SWAP不要
```

になります。

---

# transpilation最適化の重要性

Qiskitでは：

次の操作が可能です：

```python
transpile(circuit, backend)
```

これで：

ハードウェアに最適化された回路

が生成されます。

---

# Noise-adaptive transpilationとは？

高度な方法：

```text
誤差の小さいqubitを選ぶ
```

です。

つまり：

```text
best qubit selection
```

を行います。

---

# Readout error mitigationとの統合

測定誤差は：

補正できます：

```text
観測値
↓
補正行列
↓
補正結果
```

これは：

Noise-aware設計の基本です。

---

# Zero-noise extrapolationの統合

前回学んだ：

Zero-noise extrapolation：

```text
noise ↑
↓
外挿
↓
noise → 0
```

です。

これは：

VQAと相性が良いです。

---

# Ansatz depth最適化

重要原則：

```text
depth = 最小限
```

です。

理由：

```text
depth ↑
↓
noise ↑
```

だからです。

---

# Expressibilityとのトレードオフ

問題：

```text
depth ↑ → expressibility ↑
depth ↑ → noise ↑
```

です。

したがって：

最適バランスが必要です。

---

# Noise-aware Optimizer設計

ノイズ環境では：

次が有効です：

```text
SPSA
COBYLA
```

理由：

確率的評価関数に強いからです。

---

# Shot schedulingとの統合

Noise-aware設計では：

shot数も調整します：

```text
初期探索 → 少shot
収束段階 → 多shot
```

です。

---

# Error mitigationとの統合設計

推奨構成：

```text
Hardware-efficient Ansatz
+
SPSA
+
adaptive shots
+
readout mitigation
```

です。

これは：

現在の標準VQA構成です。

---

# Noise-aware cost function設計

重要：

global cost functionより：

local cost function

が有利です。

例：

```text
⟨Z_i⟩
```

などです。

理由：

勾配が消えにくいからです。

---

# symmetry preservation戦略

例：

保存量：

```text
粒子数
スピン
パリティ
```

を維持します。

すると：

```text
探索空間削減
```

が可能になります。

---

# symmetry verificationとは？

測定後：

対称性違反状態を

除外します：

```text
invalid state → discard
```

です。

これで：

精度が向上します。

---

# dynamical decouplingとは？

追加パルスを挿入して：

デコヒーレンスを抑えます：

```text
X — I — X — I
```

などです。

これは：

実機最適化でよく使われます。

---

# randomized compilingとは？

ノイズを：

ランダム化します：

```text
coherent noise
↓
stochastic noise
```

これで：

誤差平均化できます。

---

# Noise-aware VQEテンプレート

実践例：

```text
shallow ansatz
+
topology-aware transpile
+
SPSA
+
adaptive shots
+
readout mitigation
+
symmetry verification
```

です。

---

# Noise-aware QAOAテンプレート

例：

```text
small p
+
problem mixer
+
SPSA
+
grouped measurement
```

です。

---

# Noise-aware設計の黄金ルール

重要：

```text
理想解を探さない
実機最適解を探す
```

です。

これは：

NISQアルゴリズム設計の核心です。

---

# ここまでで何ができるようになったか？

できるようになったこと：

* Noise-aware Ansatz設計
* topology-aware transpilation理解
* readout mitigation統合理解
* symmetry verification理解
* dynamical decoupling理解
* randomized compiling理解
* optimizer統合理解

つまり：

実機向けVQA設計が完成しました 🚀

---

# 次回への接続

次回は：

**NISQアルゴリズム設計の統合フレームワーク**

を扱います。

ここで：

VQE・QAOA・VarQRTEなどを

統一視点で整理します。

シリーズ全体の構造が見える段階に入ります 📘

---

# 本章のまとめ

今回は：

* Noise-aware設計とは何か
* topology-aware回路設計
* readout mitigation
* symmetry verification
* dynamical decoupling
* randomized compiling
* optimizer統合戦略

を整理しました。

次回は：

NISQアルゴリズムの

**統合設計フレームワーク**

に進みます。
