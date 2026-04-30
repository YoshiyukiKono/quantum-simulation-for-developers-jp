以下は **フラットな Markdown** です。そのまま
`doc/18_potential_energy_curve.md` として保存できます。

---

# doc/18_potential_energy_curve.md

# 第18回 VQEでポテンシャルエネルギー曲線を描く

## はじめに

前回は：

* H2分子の定義
* 分子Hamiltonianの生成
* Jordan–Wigner変換
* EfficientSU2 Ansatz
* Estimatorの役割
* Optimizerの設定
* VQEの実行方法
* 基底状態エネルギーの取得

を整理しました。

今回は：

原子間距離を変化させながら

**ポテンシャルエネルギー曲線（Potential Energy Curve）**

を計算します。

これは：

量子化学シミュレーションの基本中の基本

です。

---

# ポテンシャルエネルギー曲線とは何か？

ポテンシャルエネルギー曲線とは：

原子間距離とエネルギーの関係

を表した曲線です：

```text
距離 R
↓
エネルギー E(R)
```

この関係を調べることで：

分子の安定構造

が分かります。

---

# なぜ重要なのか？

理由：

エネルギーが最小になる距離

が

結合距離

だからです。

つまり：

分子構造を決定できます。

---

# 今回の計算の流れ

今回のアルゴリズム：

```text
距離 R を設定
↓
Hamiltonian を生成
↓
Jordan–Wigner変換
↓
VQE実行
↓
エネルギー取得
↓
繰り返す
```

これを複数回実行します。

---

# 必要なライブラリ

まず：

ライブラリを読み込みます：

```python
import numpy as np
import matplotlib.pyplot as plt

from qiskit_algorithms.minimum_eigensolvers import VQE
from qiskit_algorithms.optimizers import SLSQP
from qiskit.primitives import Estimator

from qiskit.circuit.library import EfficientSU2

from qiskit_nature.second_q.drivers import PySCFDriver
from qiskit_nature.units import DistanceUnit
from qiskit_nature.second_q.mappers import JordanWignerMapper
```

---

# 距離ごとのエネルギー計算関数

まず：

距離を入力すると

エネルギーを返す関数

を作ります：

```python
def compute_energy(distance):

    driver = PySCFDriver(
        atom=f"H 0 0 0; H 0 0 {distance}",
        basis="sto3g",
        unit=DistanceUnit.ANGSTROM
    )

    problem = driver.run()

    fermionic_hamiltonian = problem.hamiltonian.second_q_op()

    mapper = JordanWignerMapper()

    qubit_hamiltonian = mapper.map(fermionic_hamiltonian)

    ansatz = EfficientSU2(
        num_qubits=qubit_hamiltonian.num_qubits,
        reps=1
    )

    estimator = Estimator()

    optimizer = SLSQP(maxiter=100)

    vqe = VQE(
        estimator=estimator,
        ansatz=ansatz,
        optimizer=optimizer
    )

    result = vqe.compute_minimum_eigenvalue(
        operator=qubit_hamiltonian
    )

    return result.eigenvalue.real
```

---

# 距離の範囲を設定する

次に：

距離のリストを作ります：

```python
distances = np.linspace(0.2, 2.0, 10)
```

ここでは：

0.2 Å ～ 2.0 Å

まで変化させます。

---

# エネルギーを計算する

各距離について：

VQEを実行します：

```python
energies = []

for d in distances:
    energy = compute_energy(d)
    energies.append(energy)
    print(f"distance = {d:.2f}, energy = {energy:.6f}")
```

---

# ポテンシャルエネルギー曲線を描く

結果を可視化します：

```python
plt.plot(distances, energies, marker="o")

plt.xlabel("H-H distance (Å)")
plt.ylabel("Energy (Hartree)")
plt.title("Potential Energy Curve of H2 (VQE)")

plt.grid(True)
plt.show()
```

これで：

ポテンシャルエネルギー曲線

が描けます。

---

# 曲線の読み方

グラフの：

最小値

が

安定な結合距離

です。

H2では通常：

```text
約 0.735 Å
```

付近になります。

---

# なぜ距離でエネルギーが変わるのか？

理由：

電子と原子核の相互作用

が変化するからです。

距離が：

近すぎる → 反発が強い
遠すぎる → 結合が弱い

となります。

その結果：

中間距離で最小になります。

---

# VQEによる量子化学の基本構造

ここまでの処理：

```text
距離 R
↓
分子Hamiltonian生成
↓
Pauli変換
↓
VQE
↓
エネルギー取得
```

を繰り返しました。

これは：

量子化学計算の標準構造

です。

---

# 古典計算との比較

従来の量子化学では：

Hartree–Fock
Configuration Interaction
Coupled Cluster

などを使います。

VQEは：

それらの量子版

と考えられます。

---

# 精度を改善する方法

改善方法：

より良いAnsatz
より良いOptimizer
Active space最適化
UCC Ansatz使用

などがあります。

---

# UCC Ansatzを使う場合

量子化学では：

通常：

```text
UCCSD
```

が使われます。

これは：

Single excitation
Double excitation

を含むAnsatzです。

---

# 実機実行との関係

今回のコードは：

Simulator

でも：

実機

でも実行可能です。

ただし：

実機ではノイズの影響があります。

---

# ここまでで何ができるようになったか？

できるようになったこと：

* 分子Hamiltonian生成
* Qubit Hamiltonian変換
* VQE実装
* 基底状態エネルギー取得
* ポテンシャルエネルギー曲線計算

つまり：

基本的な量子化学シミュレーション

が実装できました。

---

# 次回への接続

次回は：

**UCC Ansatz**

を導入して：

量子化学向けVQE

を実装します。

---

# 本章のまとめ

今回は：

* ポテンシャルエネルギー曲線とは何か
* 距離依存エネルギーの意味
* 距離ごとのVQE実行
* エネルギー曲線の描画
* 結合距離の読み取り
* 精度改善方法

を整理しました。

これで：

量子化学シミュレーションの基本ワークフロー

が完成しました。

次回は：

UCC Ansatzを使った

本格的な量子化学VQE

を実装します。
