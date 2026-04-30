# 開発者のための量子シミュレーション実践入門

## PythonとQiskitで学ぶHamiltonian simulation

本シリーズでは：

量子コンピュータの本来の用途である

**量子系のシミュレーション（Hamiltonian simulation）**

を開発者視点で体系的に学びます。

対象読者：

* VQEの理解を深めたい
* QAOAの数理構造を理解したい
* 量子化学へ進みたい
* 位相推定（QPE）を理解したい
* FTQCアルゴリズムへの橋渡しをしたい

本シリーズは：

理論
実装
応用

を段階的に接続します。

---

# Part 1 Hamiltonianとは何か

量子シミュレーションの中心概念：

**Hamiltonian**

を理解します。

* [第01回 Hamiltonianとは何か](/doc/01_what_is_hamiltonian.md)
* [第02回 Pauli演算子によるHamiltonian表現](/doc/02_pauli_hamiltonian.md)
* [第03回 物理モデルとしてのHamiltonian](/doc/03_physical_models.md)

---

# Part 2 時間発展の理解

量子計算の核心：

時間発展演算子

を扱います。

* [第04回 時間発展演算子とは何か](/doc/04_time_evolution_operator.md)
* [第05回 シュレディンガー方程式と量子回路](/doc/05_schrodinger_equation.md)

---

# Part 3 Hamiltonian simulation の基本技術

ここから実装に入ります。

* [第06回 Trotter分解](/doc/06_trotter_decomposition.md)
* [第07回 Qiskitでの時間発展シミュレーション](/doc/07_qiskit_time_evolution.md)
* [第08回 多体系シミュレーション](/doc/08_many_body_simulation.md)

---

# Part 4 Variational quantum simulation

VQEと直接つながる重要領域です。

* [第09回 Variational simulationとは何か](/doc/09_variational_simulation.md)
* [第10回 Variational time evolution の実装](/doc/10_variational_time_evolution.md)
* [第11回 VQEとの関係](/doc/11_connection_to_vqe.md)

---

# Part 5 固有値問題と位相推定

FTQCアルゴリズムへの橋渡しです。

* [第12回 固有値問題とは何か](/doc/12_eigenvalue_problem.md)
* [第13回 位相推定アルゴリズム（QPE）](/doc/13_quantum_phase_estimation.md)
* [第14回 Hamiltonian simulation と QPE の統合](/doc/14_hamiltonian_and_qpe.md)

---

# Part 6 応用への接続

量子化学・材料科学への入口です。

* [第15回 量子化学への応用](/doc/15_quantum_chemistry.md)
* [第16回 Qiskit Nature 入門](/doc/16_qiskit_nature.md)
* [第17回 材料シミュレーションへの入口](/doc/17_material_simulation.md)

---

# Part 7 NISQ時代の現実解

実機制約の中での実践方法を扱います。

* [第18回 ノイズ下でのHamiltonian simulation](/doc/18_noise_in_simulation.md)
* [第19回 実機での時間発展実験](/doc/19_real_device_simulation.md)

---

# Part 8 FTQC時代への橋渡し

将来の量子計算へ進みます。

* [第20回 Fault-tolerant Hamiltonian simulation](/doc/20_fault_tolerant_simulation.md)
* [第21回 量子シミュレーションの将来](/doc/21_future_of_quantum_simulation.md)

---

# このシリーズで得られる能力

完走すると：

* Hamiltonianが読める
* 時間発展演算子が理解できる
* Trotter分解を書ける
* Variational simulationが実装できる
* QPEの意味が理解できる
* 量子化学への入口に立てる

ようになります。

---

# 前提知識

推奨：

* Python
* NumPy
* 線形代数（行列・固有値）
* Qiskit基礎
* 変分量子アルゴリズム（VQE / QAOA）

前シリーズ：

**開発者のための量子アルゴリズム実践入門
PythonとQiskitで学ぶNISQアルゴリズム**

の修了を想定しています。
