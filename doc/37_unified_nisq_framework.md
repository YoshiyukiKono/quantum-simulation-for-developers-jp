以下は **フラットな Markdown** です。そのまま
`doc/37_unified_nisq_framework.md` として保存できます。

---

# doc/37_unified_nisq_framework.md

# 第37回 NISQアルゴリズム統合フレームワーク ― VQE・QAOA・VarQRTEを一つの視点で理解する

## はじめに

前回は：

* Noise-aware Ansatz設計
* topology-aware transpilation
* readout mitigation
* symmetry verification
* dynamical decoupling
* randomized compiling
* optimizer統合戦略

を整理しました。

ここまでで：

実機で動くVQA設計

が完成しました。

今回は：

シリーズ全体を統合する重要な視点：

**NISQアルゴリズム統合フレームワーク**

を扱います。

テーマ：

```text
VQE
QAOA
VarQRTE
VarQITE
```

を一つの構造として理解します。

---

# なぜ統合フレームワークが必要なのか？

これまで：

異なるアルゴリズムとして学んできました：

```text
VQE = 固有値問題
QAOA = 組合せ最適化
VarQRTE = 時間発展
VarQITE = 基底状態探索
```

しかし実際には：

すべて同じ構造を持っています。

---

# NISQアルゴリズムの共通構造

基本構造：

```text
パラメータ付き回路
↓
観測量評価
↓
古典最適化
↓
更新
```

つまり：

すべて

**Variational Quantum Algorithm**

です。

---

# Variational Quantum Algorithmの一般形

一般形式：

```text
|ψ(θ)⟩ = U(θ)|0⟩
```

目的：

```text
minimize ⟨ψ(θ)|O|ψ(θ)⟩
```

ここで：

O = 観測量

です。

---

# VQEの位置づけ

VQEでは：

```text
O = Hamiltonian
```

です。

つまり：

```text
minimize ⟨H⟩
```

を解きます。

これは：

基底状態探索

です。

---

# QAOAの位置づけ

QAOAでは：

```text
O = cost Hamiltonian
```

です。

つまり：

```text
最適解探索
```

になります。

構造的には：

VQEと同じです。

---

# VarQRTEの位置づけ

VarQRTEでは：

目的が変わります：

```text
時間発展近似
```

です。

つまり：

```text
|ψ(t)⟩ ≈ |ψ(θ(t))⟩
```

を求めます。

---

# VarQITEの位置づけ

VarQITEでは：

虚時間発展を使います：

```text
t → −iτ
```

すると：

```text
基底状態へ収束
```

します。

つまり：

VQEと同じ目的になります。

---

# すべてを統合すると？

統合構造：

```text
状態 = U(θ)|0⟩
↓
観測量 O
↓
⟨O⟩最小化
```

これだけです。

---

# 違いはどこにあるのか？

違いは：

次の3つだけです：

| 要素          | 内容   |
| ----------- | ---- |
| Ansatz      | 回路構造 |
| Observable  | 評価対象 |
| Update rule | 更新方法 |

です。

---

# Ansatzの違い

例：

| アルゴリズム  | Ansatz              |
| ------- | ------------------- |
| VQE     | EfficientSU2        |
| QAOA    | Cost + Mixer        |
| VarQRTE | 時間発展Ansatz          |
| VarQITE | imaginary evolution |

つまり：

回路設計が異なります。

---

# Observableの違い

例：

| アルゴリズム  | Observable    |
| ------- | ------------- |
| VQE     | Hamiltonian   |
| QAOA    | cost function |
| VarQRTE | 時間依存量         |
| VarQITE | エネルギー         |

評価対象が異なります。

---

# Update ruleの違い

例：

| アルゴリズム  | 更新方法                |
| ------- | ------------------- |
| VQE     | optimizer           |
| QAOA    | optimizer           |
| VarQRTE | 微分方程式               |
| VarQITE | imaginary evolution |

つまり：

更新式が違うだけです。

---

# 統一表現

統合モデル：

```text
θ ← θ − η M⁻¹ g
```

ここで：

| 記号 | 意味     |
| -- | ------ |
| g  | 勾配     |
| M  | 幾何テンソル |

です。

---

# McLachlan変分原理の役割

VarQRTE・VarQITEでは：

次を最小化します：

```text
|| d|ψ⟩/dt − H|ψ⟩ ||
```

これにより：

更新式が決まります。

---

# VQEとの関係

実は：

VQEも同じ構造です：

```text
minimize ⟨H⟩
```

つまり：

変分原理に従っています。

---

# QAOAとの関係

QAOAは：

離散時間版

VarQRTEです：

```text
exp(-iγH_C)
exp(-iβH_M)
```

を交互適用します。

---

# 連続時間と離散時間

比較：

| 方法      | 種類 |
| ------- | -- |
| VarQRTE | 連続 |
| QAOA    | 離散 |

つまり：

QAOAは

時間発展の近似です。

---

# imaginary-timeとの関係

VarQITE：

```text
exp(-τH)
```

です。

これは：

基底状態へ収束します。

つまり：

VQEと同じ目的です。

---

# 統合フレームワーク図

全体構造：

```text
Variational Ansatz
        ↓
Observable expectation
        ↓
Parameter update
        ↓
Iteration
```

これが：

NISQアルゴリズムの共通構造です。

---

# 開発者視点での統合理解

重要：

アルゴリズムを

個別に覚える必要はありません。

理解すべきは：

```text
Ansatz設計
Observable設計
Optimizer設計
```

の3点です。

---

# 実装レベルの統一構造

Qiskitでは：

すべて次で書けます：

```python
ansatz
observable
optimizer
```

だけです。

---

# 設計テンプレート

一般テンプレート：

```text
問題定義
↓
Hamiltonian構築
↓
Ansatz選択
↓
Observable評価
↓
Optimizer更新
```

です。

---

# NISQアルゴリズム設計の黄金フレーム

最重要構造：

```text
Problem
↓
Hamiltonian
↓
Ansatz
↓
Measurement
↓
Optimization
```

です。

---

# ここまでで何ができるようになったか？

できるようになったこと：

* VQEの統合理解
* QAOAの統合理解
* VarQRTEの統合理解
* VarQITEの統合理解
* McLachlan原理の位置づけ理解
* 時間発展との関係理解
* imaginary-timeとの関係理解

つまり：

NISQアルゴリズム全体構造が見えるようになりました 🚀

---

# 次回への接続

次回は：

**量子優位性（Quantum Advantage）の現実**

を扱います。

ここで：

どの問題で量子計算が本当に強いのか？

を整理します。

シリーズはいよいよ：

理論的到達点

に入ります。

---

# 本章のまとめ

今回は：

* NISQアルゴリズムの共通構造
* VQE・QAOA・VarQRTE・VarQITEの統一理解
* Observableの役割
* Ansatzの役割
* Optimizerの役割
* McLachlan変分原理

を整理しました。

次回は：

量子アルゴリズムの限界と可能性：

**Quantum Advantage**

に進みます。
