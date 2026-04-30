以下は **フラットな Markdown** です。そのまま
`doc/25_vqe_qaoa_unified.md` として保存できます。

---

# doc/25_vqe_qaoa_unified.md

# 第25回 VQEとQAOAの統一理解 ― NISQアルゴリズム設計の共通構造

## はじめに

前回は：

* Mixer Hamiltonianとは何か
* 標準Mixerの意味
* 制約保存Mixer
* XY Mixer
* Subspace Mixer
* Problem-informed Ansatz設計

を整理しました。

今回は：

これまで扱ってきた2つの代表的NISQアルゴリズム：

* VQE
* QAOA

を統一的に理解します。

ここで：

**NISQアルゴリズムの共通設計原理**

が見えてきます。

---

# なぜ統一理解が重要なのか？

これまで：

* VQE（量子化学）
* QAOA（最適化問題）

を別のアルゴリズムとして扱ってきました。

しかし実際には：

同じ構造

を持っています。

つまり：

```text
NISQアルゴリズム = Parameterized Quantum Circuit + Classical Optimization
```

です。

---

# NISQアルゴリズムの基本構造

すべてのNISQアルゴリズムは：

次の形になります：

```text
① Hamiltonianを定義
↓
② Ansatzを設計
↓
③ パラメータ付き回路を実行
↓
④ 測定
↓
⑤ 古典最適化
↓
⑥ 繰り返し
```

この構造は：

VQEでもQAOAでも同じです。

---

# VQEの構造

VQEでは：

目的：

基底状態探索

です。

構造：

```text
Hamiltonian（物理系）
↓
Ansatz（EfficientSU2 / UCCSD）
↓
期待値計算
↓
最小化
```

つまり：

エネルギー最小化問題

です。

---

# QAOAの構造

QAOAでは：

目的：

最適解探索

です。

構造：

```text
Cost Hamiltonian
↓
Mixer Hamiltonian
↓
回路生成
↓
期待値最小化
```

つまり：

最適化問題の基底状態探索

です。

---

# 共通する数式構造

どちらも：

次の問題を解いています：

```text
min ⟨ψ(θ)| H |ψ(θ)⟩
```

ここで：

| 記号 | 意味          |
| -- | ----------- |
| H  | Hamiltonian |
| θ  | パラメータ       |
| ψ  | Ansatz状態    |

です。

つまり：

変分法（variational method）

です。

---

# Variational Quantum Algorithmとは何か？

VQEとQAOAは：

Variational Quantum Algorithm（VQA）

の一種です。

一般形：

```text
量子回路で状態生成
+
古典最適化
```

です。

---

# Ansatz設計の違い

比較：

| アルゴリズム | Ansatz |
| ------ | ------ |
| VQE    | 任意     |
| QAOA   | 構造化    |

つまり：

QAOAは

特定問題専用Ansatz

です。

---

# Hamiltonian設計の違い

比較：

| アルゴリズム | Hamiltonian |
| ------ | ----------- |
| VQE    | 物理系         |
| QAOA   | 目的関数        |

です。

しかし：

数学的には同じです。

---

# Mixerの意味を再解釈する

VQEでは：

Ansatzが探索空間を決めます。

QAOAでは：

Mixerが探索空間を決めます。

つまり：

```text
VQE：Ansatz = 探索構造
QAOA：Mixer = 探索構造
```

という対応関係があります。

---

# QAOAは構造化VQEである

実は：

QAOAは

特殊なVQE

として書けます。

つまり：

```text
U(θ) = exp(-iβH_M) exp(-iγH_C)
```

というAnsatzを持つ

VQE

です。

---

# なぜこの理解が重要なのか？

理由：

新しいアルゴリズムを設計できるようになる

からです。

例えば：

```text
物理問題 + QAOA構造
最適化問題 + UCC構造
```

のような設計が可能になります。

---

# 問題依存Ansatzという考え方

重要なのは：

Problem-informed Ansatz

です。

例：

| 問題  | Ansatz            |
| --- | ----------------- |
| 分子  | UCCSD             |
| 最適化 | QAOA              |
| 多体系 | Trotter evolution |

です。

---

# NISQアルゴリズム設計の基本戦略

設計の流れ：

```text
① 問題をHamiltonian化
② 対応するAnsatz設計
③ 測定戦略決定
④ 最適化手法選択
```

これが：

標準設計プロセス

です。

---

# Hamiltonian中心主義

量子アルゴリズム設計では：

Hamiltonian

が中心です。

つまり：

```text
問題
↓
Hamiltonian
↓
量子回路
```

になります。

これは：

量子コンピューティング特有の発想です。

---

# 古典アルゴリズムとの違い

古典計算：

```text
アルゴリズム中心
```

量子計算：

```text
Hamiltonian中心
```

です。

この違いは非常に重要です。

---

# 探索空間設計の重要性

性能は：

探索空間

で決まります。

つまり：

```text
良いAnsatz → 高速収束
悪いAnsatz → 収束しない
```

になります。

---

# NISQアルゴリズムの3要素

重要要素：

```text
Hamiltonian
Ansatz
Optimizer
```

です。

この3つの組み合わせが：

性能を決定します。

---

# Optimizerの役割

Optimizerは：

パラメータ探索

を行います。

例：

```text
COBYLA
SPSA
SLSQP
Adam
```

などがあります。

特に：

SPSA

は実機向きです。

---

# 測定戦略の重要性

期待値計算には：

多数の測定

が必要です。

つまり：

```text
Shot数
Pauli grouping
測定順序
```

などが性能に影響します。

---

# VQEとQAOAの対応関係まとめ

整理：

| 要素          | VQE    | QAOA                 |
| ----------- | ------ | -------------------- |
| Hamiltonian | 物理系    | 最適化問題                |
| Ansatz      | 任意     | exp(-iβHM)exp(-iγHC) |
| 目的          | 基底状態   | 最適解                  |
| 探索構造        | Ansatz | Mixer                |

です。

---

# なぜこの理解が実践的なのか？

理由：

新しい問題に対応できる

ようになるからです。

つまり：

```text
問題を見る
↓
Hamiltonianを書く
↓
Ansatzを設計する
```

という思考ができるようになります ✨

---

# ここまでで何ができるようになったか？

できるようになったこと：

* VQEの理解
* QAOAの理解
* Mixerの役割理解
* Ansatz設計理解
* Hamiltonian設計理解
* Variational法の統一理解

つまり：

NISQアルゴリズム設計の基本構造

を理解しました 🚀

---

# 次回への接続

次回は：

**時間発展アルゴリズム（Hamiltonian Simulation）**

を扱います。

ここから：

量子シミュレーションの核心領域

に入ります。

---

# 本章のまとめ

今回は：

* VQEとQAOAの共通構造
* Variational Quantum Algorithm
* Problem-informed Ansatz
* Hamiltonian中心主義
* 探索空間設計
* Optimizerの役割
* 測定戦略

を整理しました。

次回は：

時間発展アルゴリズムとして

Trotter分解とHamiltonian simulation

を扱います。
