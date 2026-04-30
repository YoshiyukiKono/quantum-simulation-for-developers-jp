以下は **フラットな Markdown** です。そのまま
`doc/11_connection_to_vqe.md` として保存できます。

---

# doc/11_connection_to_vqe.md

# 第11回 VQEとの関係

## はじめに

前回は：

* VarQRTE の実装
* VarQITE の実装
* EfficientSU2 Ansatz
* Estimator の役割
* Imaginary-time evolution
* Variational time evolution の基本構造

を整理しました。

今回は：

Variational simulation と

**VQE（Variational Quantum Eigensolver）**

の関係を整理します。

ここは：

NISQアルゴリズム理解の中心

になります。

---

# VQEとは何か？

VQEの目的：

Hamiltonian の

基底状態エネルギー

を求めることです。

つまり：

```text
E₀ = min ⟨ψ|H|ψ⟩
```

を計算します。

---

# なぜ基底状態が重要なのか？

理由：

基底状態は：

分子構造
材料特性
化学反応
磁性

を決めるからです。

つまり：

量子化学の核心

です。

---

# VQEの基本構造

VQEは：

次のループで動きます：

① Ansatz を選ぶ
② 状態を準備
③ エネルギーを測定
④ 古典最適化
⑤ パラメータ更新

これを繰り返します。

---

# 数式で書くと

VQEは：

```text
θ* = argmin ⟨ψ(θ)|H|ψ(θ)⟩
```

を解きます。

つまり：

最小エネルギー状態を探します。

---

# Variational simulationとの共通点

共通構造：

Ansatz
Estimator
パラメータ更新

すべて同じです。

違うのは：

目的だけです。

---

# 決定的な違い

比較：

| アルゴリズム  | 目的        |
| ------- | --------- |
| VQE     | 最小エネルギー探索 |
| VarQITE | 虚時間発展     |
| VarQRTE | 実時間発展     |

つまり：

同じ枠組みの別バージョン

です。

---

# Imaginary-time evolutionとの関係

Imaginary-time evolution：

```text
exp(-Hτ)
```

を適用すると：

高エネルギー状態が減衰します。

結果：

基底状態に収束します。

つまり：

VarQITE ≒ VQE

です。

---

# VQEは離散版Imaginary-time evolution

直感的には：

VarQITE：

連続時間

VQE：

離散最適化

です。

---

# なぜこの関係が重要なのか？

理由：

VQEを理解すると：

Variational simulation

が理解できるからです。

逆に：

Variational simulation を理解すると：

VQEの本質が見えます。

---

# VQEの物理的意味

VQEは：

変分原理

に基づいています。

変分原理：

```text
⟨ψ|H|ψ⟩ ≥ E₀
```

つまり：

どんな状態でも：

基底状態エネルギー以上になる

という性質です。

---

# なぜ最小化すれば基底状態になるのか？

理由：

唯一：

最小値を与える状態

が

基底状態

だからです。

---

# Ansatzの役割

Ansatzは：

探索可能な状態空間

を決めます。

つまり：

Ansatzの性能が：

VQEの性能を決めます。

---

# Hardware-efficient ansatz

例：

EfficientSU2

特徴：

浅い回路
実装しやすい
NISQ向き

です。

---

# Problem-inspired ansatz

例：

UCC（Unitary Coupled Cluster）

特徴：

物理構造を反映
量子化学向き

です。

---

# なぜProblem-inspired ansatzが強いのか？

理由：

探索空間が：

物理的に意味のある領域

に限定されるからです。

つまり：

収束が速い

です。

---

# VQEのアルゴリズム構造

VQEは：

量子＋古典

のハイブリッド計算です。

流れ：

量子回路 → 測定 → 古典最適化 → 更新

を繰り返します。

---

# なぜ古典計算が必要なのか？

理由：

パラメータ最適化は：

古典計算の方が得意

だからです。

---

# 勾配ベース最適化

例：

Gradient descent
Adam
L-BFGS

などが使えます。

---

# 勾配なし最適化

例：

COBYLA
NELDER–MEAD

などがあります。

これは：

ノイズに強い

という特徴があります。

---

# VQEの課題

代表的な問題：

Barren plateau

です。

---

# Barren plateauとは何か？

問題：

勾配がゼロに近づく

現象です。

結果：

学習できなくなります。

---

# なぜ起きるのか？

理由：

状態空間が

指数的に大きい

からです。

---

# 回避方法

代表例：

浅い回路
Problem-inspired ansatz
良い初期値

です。

---

# Variational simulationとの統一的理解

まとめると：

Variational simulation は：

次の統一構造を持ちます：

```text
Ansatz + Estimator + Classical optimizer
```

この枠組みの中で：

VQE
VarQITE
VarQRTE

が動いています。

---

# Hamiltonian simulationとの関係

関係：

| 方法           | 特徴     |
| ------------ | ------ |
| Trotter法     | 厳密時間発展 |
| Variational法 | 近似時間発展 |
| VQE          | 基底状態探索 |

つまり：

すべて：

Hamiltonianを扱う方法

です。

---

# 量子アルゴリズムの統一構造

ここまで学んだ内容は：

実はすべて：

同じ構造を持っています。

対象：

Hamiltonian

目的：

時間発展
固有値問題
基底状態探索

です。

---

# なぜこれが重要なのか？

理由：

量子アルゴリズムの中心は：

Hamiltonian

だからです。

---

# 次に進むテーマ

ここまでで：

Variational simulation と VQE

の関係が整理できました。

次回は：

**固有値問題とは何か**

を扱います。

ここから：

量子位相推定（QPE）

へ進みます。

---

# 本章のまとめ

今回は：

* VQEの目的
* Imaginary-time evolutionとの関係
* Variational simulationとの統一構造
* Ansatzの役割
* 古典最適化の重要性
* Barren plateau問題
* Hamiltonian中心のアルゴリズム構造

を整理しました。

これで：

Variationalアルゴリズムの位置づけ

が明確になりました。

次回は：

固有値問題

から：

位相推定アルゴリズム

へ進みます。
