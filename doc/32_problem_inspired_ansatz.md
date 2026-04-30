以下は **フラットな Markdown** です。そのまま
`doc/32_problem_inspired_ansatz.md` として保存できます。

---

# doc/32_problem_inspired_ansatz.md

# 第32回 Problem-inspired Ansatz ― 問題構造を活かす量子回路設計

## はじめに

前回は：

* Ansatzとは何か
* Hardware-efficient Ansatz
* depth設計
* expressibility
* barren plateau
* entanglementパターン設計
* Warm start

を整理しました。

Hardware-efficient Ansatzは：

実機で動かしやすい

という利点があります。

しかし：

問題構造を使っていません。

そこで登場するのが：

**Problem-inspired Ansatz**

です。

---

# Problem-inspired Ansatzとは何か？

Problem-inspired Ansatzとは：

解きたい問題の構造を

回路に直接埋め込む方法

です。

つまり：

```text
問題の物理構造
↓
回路構造に変換
```

です。

---

# なぜProblem-inspired Ansatzが必要なのか？

理由：

探索空間を縮小できる

からです。

つまり：

```text
無関係な状態を探索しない
```

という利点があります。

---

# Hardware-efficientとの違い

比較：

| Ansatz             | 特徴    |
| ------------------ | ----- |
| Hardware-efficient | 実装が簡単 |
| Problem-inspired   | 精度が高い |

つまり：

```text
実装容易性 vs 解探索効率
```

の違いです。

---

# 代表例① QAOA Ansatz

最も有名なProblem-inspired Ansatz：

QAOA

です。

構造：

```text
問題Hamiltonian
+
Mixer Hamiltonian
```

を交互に適用します。

---

# QAOA Ansatzの形

一般形：

```text
|ψ(β,γ)⟩ =
U_M(β_p)U_C(γ_p)
...
U_M(β₁)U_C(γ₁)|+⟩
```

ここで：

* U_C = 問題Hamiltonian
* U_M = mixer

です。

---

# なぜこれがProblem-inspiredなのか？

理由：

Hamiltonianそのものが

問題を表現しているからです。

つまり：

```text
問題構造 = 回路構造
```

になります。

---

# QAOAの直感

QAOAは：

次を繰り返します：

```text
問題方向へ進む
↓
探索空間を広げる
↓
問題方向へ進む
```

つまり：

探索と収束の交互操作

です。

---

# 代表例② UCC Ansatz（化学計算）

量子化学では：

UCC（Unitary Coupled Cluster）

Ansatzが使われます。

形式：

```text
|ψ⟩ = e^{T - T†}|ψ₀⟩
```

ここで：

T = 励起演算子

です。

---

# なぜUCCが重要なのか？

理由：

電子構造問題に特化している

からです。

つまり：

```text
物理法則を直接利用
```

しています。

---

# UCCの直感

電子状態は：

励起操作の重ね合わせ

で表現できます：

```text
基底状態
↓
1電子励起
↓
2電子励起
```

これを回路化します。

---

# 代表例③ ADAPT-VQE

ADAPT-VQEは：

Ansatzを自動構築する方法

です。

処理：

```text
候補演算子集合
↓
最も重要な演算子選択
↓
回路追加
↓
繰り返し
```

となります。

---

# なぜADAPT-VQEが重要なのか？

理由：

必要な回路だけ作る

からです。

つまり：

```text
最小回路
最大精度
```

を実現します。

---

# 代表例④ Problem Hamiltonian Evolution

別の方法：

問題Hamiltonianで直接時間発展

します：

```text
e^{-iHt}
```

これは：

QAOAの一般化と考えられます。

---

# なぜ時間発展がAnsatzになるのか？

理由：

最適解は：

基底状態

だからです。

つまり：

```text
時間発展
→ 基底状態へ接近
```

します。

---

# Mixer設計の重要性

QAOAでは：

Mixer選択が重要です。

標準：

```text
Σ X_i
```

ですが：

制約付き問題では：

専用Mixer

が必要になります。

---

# Constraint-preserving Ansatz

制約付き最適化では：

制約を保つ回路

を設計します。

例：

```text
粒子数保存
スピン保存
重み保存
```

です。

---

# なぜ制約保存が重要なのか？

理由：

探索空間削減

です。

つまり：

```text
無効解を探索しない
```

になります。

---

# Mixerの例（粒子数保存）

例：

XY mixer：

```text
X_i X_j + Y_i Y_j
```

これは：

粒子数を保存します。

---

# 問題構造をどう回路へ変換するか？

基本手順：

```text
問題定式化
↓
Hamiltonian化
↓
演算子分解
↓
回路構築
```

です。

---

# Hamiltonian設計が中心になる理由

Problem-inspired Ansatzでは：

Hamiltonianが核心

になります。

つまり：

```text
問題 = Hamiltonian
```

です。

---

# Hardware-efficientとの組み合わせ

実際には：

混合することが多いです：

```text
Problem-inspired
+
Hardware-efficient
```

です。

理由：

実機制約があるためです。

---

# Hybrid Ansatzとは何か？

Hybrid Ansatz：

問題構造

＋

ハードウェア構造

を組み合わせた設計です。

つまり：

```text
精度
+
実装性
```

を両立します。

---

# Ansatz設計の戦略まとめ

基本方針：

```text
まず Problem-inspired
難しければ Hardware-efficient
最後に Hybrid
```

です。

---

# QiskitでのQAOA Ansatz例

例：

```python
from qiskit.circuit.library import QAOAAnsatz

ansatz = QAOAAnsatz(cost_operator, reps=2)
```

これだけで：

Problem-inspired Ansatz

が構築できます。

---

# UCC Ansatzの例

例：

```python
from qiskit_nature.second_q.circuit.library import UCCSD
```

電子構造問題で使用できます。

---

# Problem-inspired Ansatzの利点

利点：

```text
探索効率が高い
少ないパラメータ
高精度
収束が速い
```

です。

---

# 欠点

欠点：

```text
設計が難しい
問題依存
一般化できない
```

です。

---

# ここまでで何ができるようになったか？

できるようになったこと：

* Problem-inspired Ansatz理解
* QAOA Ansatz理解
* UCC Ansatz理解
* ADAPT-VQE理解
* Constraint-preserving Ansatz理解
* Mixer設計理解
* Hybrid Ansatz理解

つまり：

問題構造を活かした回路設計

ができるようになりました 🚀

---

# 次回への接続

次回は：

**Barren Plateau問題と回避戦略**

を扱います。

ここで：

変分量子アルゴリズム最大の難所

に入ります。

---

# 本章のまとめ

今回は：

* Problem-inspired Ansatzとは何か
* QAOA Ansatz
* UCC Ansatz
* ADAPT-VQE
* Constraint-preserving Ansatz
* Mixer設計
* Hybrid Ansatz

を整理しました。

次回は：

変分アルゴリズムの最大の障害：

**Barren Plateau問題**

とその回避方法

を扱います。
