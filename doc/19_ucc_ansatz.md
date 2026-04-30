以下は **フラットな Markdown** です。そのまま
`doc/19_ucc_ansatz.md` として保存できます。

---

# doc/19_ucc_ansatz.md

# 第19回 UCC Ansatz ― 量子化学向けVQEの標準手法

## はじめに

前回は：

* ポテンシャルエネルギー曲線とは何か
* 原子間距離とエネルギーの関係
* 距離ごとのHamiltonian生成
* VQEによるエネルギー計算
* 曲線の可視化
* 結合距離の読み取り

を整理しました。

今回は：

量子化学VQEで最も重要なAnsatz：

**UCC（Unitary Coupled Cluster）Ansatz**

を扱います。

これは：

量子化学VQEの標準手法

です。

---

# なぜUCC Ansatzが必要なのか？

前回は：

EfficientSU2

を使いました。

しかし：

EfficientSU2は

物理構造を反映していません。

つまり：

探索空間が広すぎる

という問題があります。

---

# Problem-inspired Ansatzとは何か？

量子化学では：

電子励起構造

が重要です。

そこで：

電子励起を直接表現するAnsatz

を使います。

これが：

UCC Ansatz

です。

---

# Coupled Clusterとは何か？

Coupled Cluster（CC）は：

古典量子化学で最も成功している方法の一つです。

基本形：

```text
|ψ⟩ = exp(T)|ψ₀⟩
```

ここで：

ψ₀ = Hartree–Fock状態
T = 励起演算子

です。

---

# なぜUnitaryにするのか？

通常のCC法：

```text
exp(T)
```

は：

ユニタリではありません。

量子回路では：

ユニタリ操作

が必要です。

そこで：

次の形を使います：

```text
exp(T − T†)
```

これが：

Unitary Coupled Cluster

です。

---

# UCC Ansatzの構造

基本形：

```text
|ψ⟩ = exp(T − T†)|ψ₀⟩
```

ここで：

T = 励起演算子

です。

---

# 励起演算子とは何か？

励起とは：

電子の軌道移動

です。

例：

```text
a†_a a_i
```

意味：

i軌道 → a軌道

へ電子が移動します。

---

# Single excitation

単一励起：

```text
a†_a a_i
```

1電子が移動します。

---

# Double excitation

二重励起：

```text
a†_a a†_b a_j a_i
```

2電子が同時に移動します。

---

# UCCSDとは何か？

UCCSD：

Single excitation
Double excitation

を含むAnsatzです：

```text
T = T₁ + T₂
```

ここで：

T₁ = single excitation
T₂ = double excitation

です。

---

# なぜUCCSDが重要なのか？

理由：

精度と計算コストのバランス

が良いからです。

そのため：

量子化学VQEの標準

になっています。

---

# QiskitでUCC Ansatzを使う

Qiskit Natureでは：

UCC Ansatzが実装されています：

```python
from qiskit_nature.second_q.circuit.library import UCCSD
```

---

# Hartree–Fock状態の準備

まず：

初期状態を準備します：

```python
from qiskit_nature.second_q.circuit.library import HartreeFock
```

---

# 必要な情報を取得する

問題設定：

```python
num_particles = problem.num_particles
num_spatial_orbitals = problem.num_spatial_orbitals
```

---

# UCCSD Ansatzを構築する

コード：

```python
from qiskit_nature.second_q.circuit.library import UCCSD
from qiskit_nature.second_q.mappers import JordanWignerMapper

mapper = JordanWignerMapper()

initial_state = HartreeFock(
    num_spatial_orbitals,
    num_particles,
    mapper
)

ansatz = UCCSD(
    num_spatial_orbitals=num_spatial_orbitals,
    num_particles=num_particles,
    mapper=mapper,
    initial_state=initial_state
)
```

これで：

量子化学向けAnsatz

が構築できます。

---

# EfficientSU2との違い

比較：

| Ansatz       | 特徴     |
| ------------ | ------ |
| EfficientSU2 | 汎用     |
| UCCSD        | 量子化学向き |

つまり：

UCCSDの方が

物理的に意味のある探索

ができます。

---

# なぜ収束が速くなるのか？

理由：

探索空間が：

電子励起構造

に限定されるからです。

つまり：

不要な状態を探索しません。

---

# VQEにUCCSDを組み込む

前回のVQEコードを：

次のように変更します：

```python
vqe = VQE(
    estimator=estimator,
    ansatz=ansatz,
    optimizer=optimizer
)
```

これだけです。

---

# 計算結果の違い

EfficientSU2：

収束が遅い場合がある

UCCSD：

収束が速い
精度が高い

という特徴があります。

---

# なぜ量子化学に特化できるのか？

理由：

電子励起モデル

を直接使っているからです。

つまり：

物理モデルをAnsatzに埋め込んでいる

ということです。

---

# UCC Ansatzの課題

課題：

回路が深くなる

という問題があります。

理由：

励起演算子が多いからです。

---

# Trotter分解との関係

UCC Ansatzは：

実際には：

```text
exp(T − T†)
```

を

Trotter分解

で実装します。

つまり：

近似回路になります。

---

# 実機実行との関係

UCCSDは：

NISQでも実行可能ですが：

回路がやや深くなります。

そのため：

Active space選択

が重要になります。

---

# Active spaceとの関係

Active spaceとは：

重要な軌道だけ選択する方法

です。

これにより：

回路サイズを削減できます。

---

# ここまでで何ができるようになったか？

できるようになったこと：

* 分子Hamiltonian生成
* Pauli変換
* VQE実装
* ポテンシャル曲線計算
* UCCSD Ansatz構築

つまり：

量子化学VQEの基本構造

が完成しました。

---

# 次回への接続

次回は：

**Active space近似**

を導入して：

より実用的な量子化学計算

へ進みます。

---

# 本章のまとめ

今回は：

* Coupled Cluster法
* Unitary Coupled Cluster
* Single excitation
* Double excitation
* UCCSD Ansatz
* Hartree–Fock初期状態
* EfficientSU2との違い
* Active spaceとの関係

を整理しました。

これで：

量子化学VQEの標準Ansatz

が使えるようになりました。

次回は：

Active spaceを使った

量子ビット削減技術

を扱います。
