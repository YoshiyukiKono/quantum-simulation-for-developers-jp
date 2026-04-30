以下は **フラットな Markdown** です。そのまま
`doc/10_variational_time_evolution.md` として保存できます。

---

# doc/10_variational_time_evolution.md

# 第10回 Variational time evolution の実装

## はじめに

前回は：

* Variational simulationとは何か
* McLachlan変分原理
* Ansatzの役割
* パラメータ更新の仕組み
* Real-time evolution
* Imaginary-time evolution
* VQEとの関係

を整理しました。

今回は：

実際に

**Qiskitを使ってVariational time evolutionを実装**

します。

つまり：

理論 → 実装

へ進みます。

---

# Variational time evolution の全体像

Variational time evolution の目的：

時間発展

```
|ψ(t)⟩ = exp(-iHt)|ψ(0)⟩
```

を：

パラメータ付き回路

```
|ψ(θ(t))⟩
```

で近似することです。

---

# Qiskitで使うクラス

Qiskitでは：

Variational time evolution は次のクラスで実装されています：

* VarQRTE（Real-time evolution）
* VarQITE（Imaginary-time evolution）

今回は：

まず VarQRTE を扱います。

---

# 必要なライブラリ

まず：

必要なモジュールを読み込みます：

```python
from qiskit.quantum_info import SparsePauliOp
from qiskit.circuit.library import EfficientSU2
from qiskit_algorithms.time_evolvers import VarQRTE
from qiskit_algorithms.state_fidelities import ComputeUncompute
from qiskit.primitives import Estimator
```

ここで重要なのは：

```
EfficientSU2
```

です。

これは：

代表的な Ansatz 回路です。

---

# Hamiltonianを定義する

まず：

簡単な例として：

横磁場Isingモデルを使います：

```
H = Z₀Z₁ + X₀ + X₁
```

コード：

```python
hamiltonian = SparsePauliOp(
    ["ZZ", "XI", "IX"],
    coeffs=[1.0, 1.0, 1.0]
)
```

---

# Ansatzを定義する

次に：

パラメータ付き回路を作ります：

```python
ansatz = EfficientSU2(2, reps=1)
```

意味：

2量子ビット
1層構造

の回路です。

---

# EfficientSU2とは何か？

EfficientSU2は：

次の構造を持つ回路です：

```
RY
RZ
CNOT
```

を繰り返すことで：

広い状態空間を探索できます。

つまり：

Variational simulation に適した Ansatz です。

---

# Estimator を準備する

次に：

期待値計算器を準備します：

```python
estimator = Estimator()
```

これは：

観測量の期待値を評価する

ために使われます。

---

# VarQRTE を構築する

次に：

Variational Real-Time Evolution を定義します：

```python
evolver = VarQRTE(
    ansatz=ansatz,
    estimator=estimator
)
```

これで：

変分時間発展の準備が整いました。

---

# 初期状態を設定する

初期パラメータ：

```python
import numpy as np

initial_parameters = np.zeros(ansatz.num_parameters)
```

これは：

回路の初期状態を決めます。

---

# 時間発展を実行する

次に：

時間発展を計算します：

```python
evolution_result = evolver.evolve(
    hamiltonian,
    initial_parameters,
    time=1.0
)
```

これで：

変分時間発展が実行されます。

---

# 結果を確認する

出力：

```python
print(evolution_result)
```

ここには：

最終パラメータ

が含まれます。

つまり：

```
θ(t)
```

が得られます。

---

# 状態ベクトルを取得する

最終状態：

```python
final_state = ansatz.assign_parameters(
    evolution_result.parameters
)
```

これで：

時間発展後の近似状態

が得られます。

---

# Trotter法との比較

ここで重要なのは：

Variational法とTrotter法の違いです。

比較：

| 方法           | 特徴      |
| ------------ | ------- |
| Trotter法     | 回路が深くなる |
| Variational法 | 回路が浅い   |
| Trotter法     | 高精度     |
| Variational法 | NISQ向き  |

つまり：

実機では Variational法 が有利です。

---

# Imaginary-time evolution の実装

次に：

VarQITE を扱います。

これは：

基底状態探索

に使われます。

---

# VarQITE を読み込む

```python
from qiskit_algorithms.time_evolvers import VarQITE
```

---

# VarQITE を構築する

```python
imaginary_evolver = VarQITE(
    ansatz=ansatz,
    estimator=estimator
)
```

---

# Imaginary-time evolution を実行する

```python
imaginary_result = imaginary_evolver.evolve(
    hamiltonian,
    initial_parameters,
    time=1.0
)
```

これで：

基底状態に近づく方向へ

状態が更新されます。

---

# なぜ基底状態に収束するのか？

Imaginary-time evolution は：

```
exp(-Hτ)
```

を近似します。

すると：

高エネルギー状態が減衰します。

つまり：

最低エネルギー状態

だけが残ります。

---

# VQEとの違い

比較：

| 方法      | 特徴     |
| ------- | ------ |
| VQE     | 最適化問題  |
| VarQITE | 時間発展問題 |

つまり：

VarQITEは

連続的な基底状態探索

です。

---

# Ansatzの選び方

Ansatz設計は重要です。

例：

EfficientSU2
Hardware-efficient ansatz
Problem-inspired ansatz

などがあります。

---

# Problem-inspired ansatzとは何か？

例：

Isingモデルなら：

```
exp(-i Z₀Z₁ θ)
```

のような構造を使います。

つまり：

Hamiltonianに合わせて設計する

方法です。

---

# Variational time evolution の実装手順まとめ

基本手順：

① Hamiltonianを定義
② Ansatzを選ぶ
③ Estimatorを準備
④ VarQRTEまたはVarQITEを作る
⑤ evolve() を実行

これで：

変分時間発展が実装できます。

---

# 実機での実行について

Variational simulation の利点：

回路が浅い

つまり：

実機で実行可能

です。

これは：

NISQアルゴリズムの核心です。

---

# 次に進むテーマ

ここまでで：

Variational time evolution

が実装できました。

次回は：

**VQEとの関係**

をさらに詳しく整理しながら：

Variational simulation の理論的位置づけ

を明確にします。

---

# 本章のまとめ

今回は：

* VarQRTE の実装
* VarQITE の実装
* EfficientSU2 Ansatz
* Estimator の役割
* 基底状態探索との関係
* VQEとの違い

を整理しました。

これで：

Variational simulation を

実際に動かす準備が整いました。

次回は：

Variational simulation と VQE の関係

を理論的に整理していきます。
