以下は **フラットな Markdown** です。そのまま
`doc/27_trotter_decomposition.md` として保存できます。

---

# doc/27_trotter_decomposition.md

# 第27回 Trotter分解 ― Hamiltonian Simulationを回路へ変換する技術

## はじめに

前回は：

* 時間発展演算子とは何か
* Schrödinger方程式との関係
* Hamiltonian Simulationの意味
* 固有状態と時間発展
* 多体系モデルとの関係
* なぜ分解が必要なのか

を整理しました。

今回は：

時間発展を量子回路として実装するための核心技術：

**Trotter分解**

を扱います。

---

# なぜTrotter分解が必要なのか？

時間発展は：

次の演算子で表されます：

```text
exp(-iHt)
```

しかし現実のHamiltonianは：

通常：

```text
H = H₁ + H₂ + H₃ + ...
```

の形をしています。

つまり：

```text
exp(-i(H₁ + H₂)t)
```

を直接実装する必要があります。

---

# 問題：指数演算子は分解できない

一般には：

```text
exp(A + B) ≠ exp(A)exp(B)
```

です。

つまり：

そのまま回路へ変換できません。

---

# 解決方法：Trotter分解

Trotter分解では：

次の近似を使います：

```text
exp(-i(H₁ + H₂)t)
≈ exp(-iH₁t)exp(-iH₂t)
```

これは：

短時間なら良い近似

になります。

---

# 分割数を増やすと精度が向上する

時間を分割すると：

```text
exp(-i(H₁ + H₂)t)
≈ (exp(-iH₁t/n)exp(-iH₂t/n))ⁿ
```

になります。

ここで：

n = Trotterステップ数

です。

---

# なぜ精度が向上するのか？

理由：

短時間では：

交換関係の影響が小さくなる

からです。

つまり：

```text
大きな時間発展
↓
小さな時間発展へ分割
↓
誤差減少
```

となります。

---

# Trotter分解の次数

代表例：

| 分解        | 精度     |
| --------- | ------ |
| 1次Trotter | 基本     |
| 2次Trotter | 高精度    |
| 高次Trotter | さらに高精度 |

です。

---

# 2次Trotter分解

2次分解は：

次の形になります：

```text
exp((A+B)t)
≈ exp(At/2)exp(Bt)exp(At/2)
```

これは：

誤差が小さくなります。

---

# Isingモデルで考える

例：

```text
H = Z₁Z₂ + X₁
```

時間発展：

```text
exp(-iHt)
```

を分解すると：

```text
exp(-iZ₁Z₂t)
exp(-iX₁t)
```

になります。

---

# Pauli演算子の指数演算

重要な事実：

Pauli演算子の指数は

量子ゲートに変換できます。

例：

```text
exp(-iθZ)
```

は：

RZ回転になります。

---

# ZZ項の指数演算

次の演算子：

```text
exp(-iθZ₁Z₂)
```

は：

2量子ビット回路で実装できます。

構造：

```text
CNOT
RZ
CNOT
```

です。

---

# なぜCNOTが必要なのか？

理由：

2量子ビット相互作用

を表現するためです。

つまり：

```text
Z₁Z₂
```

は：

エンタングルメント操作

になります。

---

# X項の指数演算

次の演算子：

```text
exp(-iθX)
```

は：

```text
RX回転
```

として実装できます。

---

# 実際の回路構築の流れ

処理：

```text
Hamiltonian分解
↓
各項を指数演算へ変換
↓
量子ゲートへ変換
↓
繰り返し適用
```

となります。

---

# QiskitでTrotter分解を使う

Qiskitでは：

時間発展回路を自動生成できます：

```python
from qiskit.quantum_info import SparsePauliOp

H = SparsePauliOp.from_list([
    ("ZZ", 1.0),
    ("XI", 0.5)
])
```

これを：

時間発展回路へ変換します。

---

# PauliEvolutionGateとは何か？

Qiskitには：

PauliEvolutionGate

があります：

```python
from qiskit.circuit.library import PauliEvolutionGate
```

これを使うと：

```python
gate = PauliEvolutionGate(H, time=1.0)
```

として：

時間発展回路を生成できます。

---

# Trotter近似の指定

分解方法は：

指定できます：

```python
from qiskit.synthesis import LieTrotter

gate = PauliEvolutionGate(
    H,
    time=1.0,
    synthesis=LieTrotter()
)
```

これで：

1次Trotter分解

になります。

---

# 分割数の設定

ステップ数：

```python
LieTrotter(reps=4)
```

とすると：

4分割されます。

つまり：

精度が向上します。

---

# なぜ繰り返し回数が重要なのか？

理由：

回数を増やすほど：

近似精度が向上

するからです。

ただし：

回路は長くなります。

---

# 精度と回路深さのトレードオフ

整理：

| reps | 精度 | 回路深さ |
| ---- | -- | ---- |
| 小    | 低  | 浅い   |
| 大    | 高  | 深い   |

です。

これは：

NISQ設計の典型問題

です。

---

# Trotter分解の応用分野

応用：

```text
量子化学
量子多体系
QAOA
量子ウォーク
量子相転移解析
```

です。

つまり：

Hamiltonian Simulationの基盤

になります。

---

# QAOAとの関係（再整理）

QAOAは：

次の操作の繰り返しです：

```text
exp(-iγH_C)
exp(-iβH_M)
```

これは：

Trotter分解と同じ構造

です。

つまり：

QAOAは

時間発展の特殊例

です。

---

# NISQアルゴリズム設計への意味

重要な理解：

```text
Hamiltonian
↓
指数演算子
↓
量子回路
```

という変換が：

量子アルゴリズム設計の核心

です。

---

# ここまでで何ができるようになったか？

できるようになったこと：

* Trotter分解の理解
* 指数演算子の分解
* Pauli回転理解
* ZZ相互作用回路理解
* Qiskitでの時間発展実装
* repsパラメータ理解

つまり：

Hamiltonian Simulationを回路へ変換

できるようになりました 🚀

---

# 次回への接続

次回は：

**Real-time evolutionとImaginary-time evolution**

を扱います。

ここで：

時間発展アルゴリズムの応用

に入ります。

---

# 本章のまとめ

今回は：

* Trotter分解とは何か
* 指数演算子の分解問題
* Pauli指数演算
* ZZ回路構造
* PauliEvolutionGate
* LieTrotter分解
* repsパラメータ

を整理しました。

次回は：

Real-time / Imaginary-time evolution

を使った量子アルゴリズム設計

へ進みます。
