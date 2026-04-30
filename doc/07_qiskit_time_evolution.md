以下は **フラットな Markdown** です。そのまま
`doc/07_qiskit_time_evolution.md` として保存できます。

---

# doc/07_qiskit_time_evolution.md

# 第07回 Qiskitでの時間発展シミュレーション

## はじめに

前回は：

* Trotter分解とは何か
* 非可換演算子の問題
* 1次Trotter分解
* 高次Suzuki–Trotter分解
* 回路深さと精度のトレードオフ
* Hamiltonian simulationの基本構造

を整理しました。

今回は：

実際に

**Qiskitを使って時間発展演算子を実装**

します。

つまり：

```text
U(t) = exp(-iHt)
```

をコードとして動かします。

ここから：

理論 → 実装

に進みます。

---

# Qiskitで時間発展を扱う方法

Qiskitでは：

時間発展は次の流れで実装します：

① Hamiltonianを定義
② Pauli演算子として表現
③ 時間発展演算子を生成
④ 回路へ変換

順番に見ていきます。

---

# Step1 必要なライブラリ

まず：

基本ライブラリを読み込みます：

```python
from qiskit import QuantumCircuit
from qiskit.quantum_info import SparsePauliOp
from qiskit.circuit.library import PauliEvolutionGate
```

ここで重要なのは：

```text
SparsePauliOp
```

です。

---

# SparsePauliOpとは何か？

SparsePauliOpは：

Hamiltonianを

Pauli分解形式

で表現するクラスです。

例えば：

```text
H = X + Z
```

を定義できます。

---

# Step2 Hamiltonianを定義する

例：

```python
H = SparsePauliOp(["X", "Z"], coeffs=[1.0, 1.0])
```

意味：

```text
H = X + Z
```

です。

これは：

Pauli分解されたHamiltonian

です。

---

# Step3 時間発展演算子を作る

次に：

時間発展演算子を生成します：

```python
evolution_gate = PauliEvolutionGate(H, time=1.0)
```

これは：

```text
exp(-iHt)
```

を表します。

ここでは：

```text
t = 1.0
```

です。

---

# Step4 回路に追加する

次に：

量子回路へ追加します：

```python
qc = QuantumCircuit(1)
qc.append(evolution_gate, [0])
qc.draw("text")
```

これで：

時間発展回路が生成されます。

---

# 実行結果のイメージ

例えば：

```text
H = X + Z
```

の場合：

回路は：

```text
RX
RZ
```

の組み合わせになります。

これは：

Trotter分解による近似です。

---

# Trotter分解の指定

より詳細に制御するには：

Trotter分解の方法を指定できます。

例：

```python
from qiskit.synthesis.evolution import LieTrotter

evolution_gate = PauliEvolutionGate(
    H,
    time=1.0,
    synthesis=LieTrotter(reps=2)
)
```

ここで：

```text
reps
```

は：

Trotterステップ数

です。

---

# repsの意味

重要な関係：

reps ↑

↓

精度 ↑
回路深さ ↑

になります。

つまり：

精度とノイズのトレードオフ

です。

---

# 2量子ビットの例

次に：

相互作用Hamiltonianを扱います：

```text
H = Z₀Z₁
```

コード：

```python
H = SparsePauliOp(["ZZ"], coeffs=[1.0])
```

時間発展：

```python
evolution_gate = PauliEvolutionGate(H, time=1.0)

qc = QuantumCircuit(2)
qc.append(evolution_gate, [0, 1])
qc.draw("text")
```

---

# 実行結果の意味

この回路は：

```text
exp(-i Z₀Z₁ t)
```

を表します。

内部では：

```text
CNOT
RZ
CNOT
```

に変換されます。

---

# 複数項Hamiltonianの例

例：

```text
H = X₀ + Z₀Z₁ + X₁
```

コード：

```python
H = SparsePauliOp(
    ["XI", "ZZ", "IX"],
    coeffs=[1.0, 1.0, 1.0]
)
```

時間発展：

```python
evolution_gate = PauliEvolutionGate(H, time=1.0)

qc = QuantumCircuit(2)
qc.append(evolution_gate, [0, 1])
qc.draw("text")
```

---

# 実際に状態を進めてみる

状態ベクトルの変化を確認できます：

```python
from qiskit.quantum_info import Statevector

initial_state = Statevector.from_label("00")
final_state = initial_state.evolve(evolution_gate)

print(final_state)
```

これで：

時間発展後の状態

が得られます。

---

# Aer Simulatorでの確認

Aerを使えば：

より詳細なシミュレーションができます：

```python
from qiskit_aer import AerSimulator
from qiskit import transpile

sim = AerSimulator()

compiled = transpile(qc, sim)
result = sim.run(compiled).result()

print(result)
```

---

# 時間パラメータを変えてみる

時間発展の特徴：

```text
t
```

を変えることで：

状態変化が変わります。

例：

```python
PauliEvolutionGate(H, time=0.1)
PauliEvolutionGate(H, time=0.5)
PauliEvolutionGate(H, time=2.0)
```

これは：

物理時間の変化

を意味します。

---

# SuzukiTrotter分解を使う

より高精度にするには：

```python
from qiskit.synthesis.evolution import SuzukiTrotter

evolution_gate = PauliEvolutionGate(
    H,
    time=1.0,
    synthesis=SuzukiTrotter(order=2, reps=2)
)
```

これで：

高次近似が可能になります。

---

# LieTrotterとの違い

違い：

LieTrotter：

```text
1次近似
```

SuzukiTrotter：

```text
高次近似
```

つまり：

Suzukiの方が高精度です。

---

# 実装の基本テンプレート

時間発展の基本構造：

```python
H = SparsePauliOp([...], coeffs=[...])

evolution_gate = PauliEvolutionGate(H, time=t)

qc = QuantumCircuit(n)
qc.append(evolution_gate, range(n))
```

これが：

標準形です。

---

# Hamiltonian simulationの実装フロー

まとめると：

① Hamiltonian定義
② Pauli分解
③ 時間発展生成
④ 回路追加
⑤ simulator実行

になります。

---

# 次のステップ

ここまでで：

単純なHamiltonian simulation

が実装できました。

次は：

より現実に近い問題：

**多体系シミュレーション**

を扱います。

---

# 本章のまとめ

今回は：

* SparsePauliOp
* PauliEvolutionGate
* LieTrotter分解
* SuzukiTrotter分解
* 2量子ビット相互作用
* 状態ベクトル時間発展
* Aer Simulatorでの検証

を実装しました。

これで：

QiskitによるHamiltonian simulation

の基本が完成しました。

次回は：

**多体系シミュレーション**

を扱いながら：

スピン鎖モデルを実装していきます。
