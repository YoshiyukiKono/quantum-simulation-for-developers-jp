以下は **フラットな Markdown** です。そのまま
`doc/23_qaoa_maxcut.md` として保存できます。

---

# doc/23_qaoa_maxcut.md

# 第23回 MaxCut問題をQAOAで解く ― 最適化問題の量子実装

## はじめに

前回は：

* QAOAとは何か
* Cost Hamiltonian
* Mixer Hamiltonian
* レイヤー構造
* Isingモデルとの関係
* VQEとの違い

を整理しました。

今回は：

最も代表的な例：

**MaxCut問題**

をQAOAで解きます。

ここで初めて：

組合せ最適化問題を量子回路として実装

します。

---

# MaxCut問題とは何か？

MaxCut問題とは：

グラフの頂点集合を2つに分割して

異なる集合に属する辺の数を最大化する問題

です。

例：

```text
A --- B
|     |
C --- D
```

このグラフを：

2グループに分けます。

目的：

切断される辺の数を最大化

することです。

---

# なぜMaxCutが重要なのか？

理由：

多くの最適化問題が

MaxCutへ変換可能

だからです。

例：

```text
ネットワーク分割
クラスタリング
スケジューリング
配置問題
```

---

# MaxCutを数式で表現する

頂点 i と j が異なる集合に属する場合：

値 = 1

同じ集合なら：

値 = 0

つまり：

目的関数は：

```text
C = Σ (1 − ZiZj)/2
```

になります。

---

# なぜZiZjが出てくるのか？

理由：

量子ビット状態：

```text
|0⟩ → +1
|1⟩ → −1
```

として扱うと：

同じ状態：

ZiZj = +1

異なる状態：

ZiZj = −1

になるからです。

---

# Cost Hamiltonianの構築

MaxCutのCost Hamiltonian：

```text
H_C = Σ (1 − ZiZj)/2
```

です。

これが：

最適化対象

になります。

---

# Mixer Hamiltonianの構築

典型的なMixer：

```text
H_M = Σ Xi
```

です。

役割：

状態の探索

を可能にします。

---

# QAOAの回路構造（MaxCut）

処理の流れ：

```text
|+⟩状態準備
↓
Cost evolution
↓
Mixer evolution
↓
測定
↓
古典最適化
```

です。

---

# 小さなグラフで試す

例：

2頂点グラフ

```text
0 --- 1
```

この場合：

Hamiltonian：

```text
H = (1 − Z0Z1)/2
```

になります。

---

# QiskitでMaxCut問題を実装する

まず：

Pauli演算子として定義します：

```python
from qiskit.quantum_info import SparsePauliOp

cost_hamiltonian = SparsePauliOp.from_list([
    ("ZZ", -0.5),
    ("II", 0.5)
])
```

---

# Samplerの準備

QAOAには：

Sampler

を使います：

```python
from qiskit.primitives import Sampler

sampler = Sampler()
```

---

# Optimizerの準備

例：

```python
from qiskit_algorithms.optimizers import COBYLA

optimizer = COBYLA()
```

---

# QAOAインスタンスを作る

コード：

```python
from qiskit_algorithms import QAOA

qaoa = QAOA(
    sampler=sampler,
    optimizer=optimizer,
    reps=1
)
```

---

# 最小固有値問題として解く

QAOAは：

基底状態探索

として実行されます：

```python
from qiskit_algorithms import MinimumEigensolverResult

result = qaoa.compute_minimum_eigenvalue(cost_hamiltonian)
```

---

# 結果を確認する

最適値：

```python
print(result.eigenvalue)
```

最適状態：

```python
print(result.eigenstate)
```

---

# なぜ基底状態を探すのか？

理由：

Cost Hamiltonianの基底状態

が

最適解

だからです。

つまり：

```text
最適化問題
↓
Hamiltonian
↓
基底状態探索
```

という構造になります。

---

# 測定結果の解釈

測定結果：

```text
00
01
10
11
```

のうち：

最大確率のビット列

が解になります。

---

# MaxCut解の読み取り

例：

```text
01
```

なら：

頂点0 → グループA
頂点1 → グループB

です。

つまり：

1本の辺が切断されています。

---

# もう少し大きなグラフへ拡張する

例：

3頂点：

```text
0 --- 1
 \   /
   2
```

Hamiltonian：

```text
(1 − Z0Z1)/2
+ (1 − Z1Z2)/2
+ (1 − Z0Z2)/2
```

になります。

---

# Pauli演算子として表現する

例：

```python
cost_hamiltonian = SparsePauliOp.from_list([
    ("ZZI", -0.5),
    ("IZZ", -0.5),
    ("ZIZ", -0.5),
    ("III", 1.5)
])
```

---

# repsパラメータの意味

設定：

```python
reps = 1
```

は：

QAOAの深さ

です。

変更：

```python
reps = 2
```

にすると：

精度が向上します。

---

# QAOAの直感的理解（再整理）

処理：

```text
Cost evolution → 良い解へ近づく
Mixer evolution → 探索を広げる
```

これを：

繰り返します。

---

# QAOAの強み

特徴：

* 回路が浅い
* 実機向き
* 任意グラフへ適用可能
* 多くの問題へ拡張可能

です。

---

# VQEとの統一的理解

VQE：

任意Ansatz

QAOA：

構造化Ansatz

つまり：

QAOAは

問題特化型VQE

と考えられます。

---

# ここまでで何ができるようになったか？

できるようになったこと：

* MaxCut問題の量子表現
* Cost Hamiltonian構築
* Mixer Hamiltonian理解
* QAOA実装
* 測定結果の解釈

つまり：

量子最適化問題を実装できるようになりました。

---

# 次回への接続

次回は：

**Mixer Hamiltonianの設計戦略**

を扱います。

ここで：

QAOAの性能を左右する核心部分

に入ります。

---

# 本章のまとめ

今回は：

* MaxCut問題とは何か
* Cost Hamiltonian構築
* Pauli表現
* QAOA実装
* 測定結果の解釈
* repsパラメータの意味

を整理しました。

次回は：

より高度なQAOA設計として

Mixer Hamiltonianの拡張

を扱います。
