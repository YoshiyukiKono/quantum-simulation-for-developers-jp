以下は **フラットな Markdown** です。そのまま
`doc/08_many_body_simulation.md` として保存できます。

---

# doc/08_many_body_simulation.md

# 第08回 多体系シミュレーション

## はじめに

前回は：

* SparsePauliOp によるHamiltonian定義
* PauliEvolutionGate の使い方
* LieTrotter分解
* SuzukiTrotter分解
* 2量子ビット相互作用の実装
* Aer Simulatorでの時間発展確認

を整理しました。

ここまでで：

単純なHamiltonian simulation

が実装できるようになりました。

今回は：

より現実に近い問題である

**多体系（many-body system）**

を扱います。

---

# 多体系とは何か？

多体系とは：

複数の粒子が相互作用する系

のことです。

例：

電子集団
スピン鎖
磁性体
分子
固体材料

これらはすべて：

多体系です。

---

# なぜ多体系が重要なのか？

理由：

現実の物質は

単一粒子では説明できない

からです。

つまり：

材料科学
量子化学
凝縮系物理

すべて：

多体系問題です。

---

# 多体系の基本モデル：スピン鎖

最も基本的な多体系モデル：

スピン鎖

です。

例：

```text
H = Z₀Z₁ + Z₁Z₂ + Z₂Z₃
```

これは：

隣接スピンの相互作用

を表します。

---

# スピン鎖の物理的意味

このモデルは：

スピンが隣同士で影響し合う

系です。

応用例：

磁性体
量子相転移
量子情報伝播

があります。

---

# 3量子ビットスピン鎖を実装する

例：

```text
H = Z₀Z₁ + Z₁Z₂
```

これをQiskitで書きます。

---

# Hamiltonianの定義

コード：

```python
from qiskit.quantum_info import SparsePauliOp

H = SparsePauliOp(
    ["ZZI", "IZZ"],
    coeffs=[1.0, 1.0]
)
```

意味：

```text
Z₀Z₁ + Z₁Z₂
```

です。

---

# 時間発展回路の生成

次に：

時間発展演算子を作ります：

```python
from qiskit.circuit.library import PauliEvolutionGate

evolution_gate = PauliEvolutionGate(H, time=1.0)
```

---

# 回路として実装する

回路：

```python
from qiskit import QuantumCircuit

qc = QuantumCircuit(3)
qc.append(evolution_gate, [0, 1, 2])

qc.draw("text")
```

これで：

3量子ビット相互作用

が実装されます。

---

# 状態の時間発展を確認する

状態ベクトル：

```python
from qiskit.quantum_info import Statevector

initial_state = Statevector.from_label("000")
final_state = initial_state.evolve(evolution_gate)

print(final_state)
```

これで：

時間発展後の状態

が確認できます。

---

# スピン鎖の時間変化を観察する

時間パラメータを変えると：

```python
PauliEvolutionGate(H, time=0.1)
PauliEvolutionGate(H, time=1.0)
PauliEvolutionGate(H, time=2.0)
```

状態の変化が異なります。

つまり：

スピンの相互作用ダイナミクス

が観察できます。

---

# 横磁場Isingモデル

より現実的なモデル：

横磁場Isingモデル

です。

例：

```text
H = Z₀Z₁ + Z₁Z₂ + X₀ + X₁ + X₂
```

これは：

相互作用 + 外部磁場

を含むモデルです。

---

# 横磁場Isingモデルの実装

コード：

```python
H = SparsePauliOp(
    ["ZZI", "IZZ", "XII", "IXI", "IIX"],
    coeffs=[1, 1, 1, 1, 1]
)
```

これで：

横磁場Isingモデル

が定義できます。

---

# 時間発展回路の生成

```python
evolution_gate = PauliEvolutionGate(H, time=1.0)

qc = QuantumCircuit(3)
qc.append(evolution_gate, [0, 1, 2])

qc.draw("text")
```

これで：

多体系時間発展

が実装されます。

---

# なぜ横磁場が重要なのか？

理由：

量子揺らぎ

が導入されるからです。

Z相互作用：

古典的モデル

X磁場：

量子的効果

を導入します。

---

# エンタングルメントの生成

多体系の特徴：

エンタングルメント

が自然に生成されます。

例：

初期状態：

```text
|000⟩
```

時間発展後：

```text
重ね合わせ状態
```

になります。

---

# エンタングルメントを確認する

例：

```python
from qiskit.quantum_info import partial_trace
```

を使うと：

部分状態を解析できます。

これにより：

量子相関が確認できます。

---

# 多体系シミュレーションの難しさ

問題：

粒子数が増える

↓

状態空間が指数的に増える

つまり：

古典計算が困難になります。

---

# なぜ量子コンピュータが必要なのか？

理由：

量子状態を

量子状態で表現できる

からです。

つまり：

自然なシミュレーションが可能になります。

---

# スピン鎖の拡張

一般形：

```text
H = Σ ZᵢZᵢ₊₁
```

量子ビット数を増やすだけで：

より大規模なモデル

が作れます。

---

# 周期境界条件

より現実的なモデル：

周期境界条件

があります。

例：

```text
H = Z₀Z₁ + Z₁Z₂ + Z₂Z₀
```

これは：

リング構造

を表します。

---

# 周期境界モデルの実装

コード：

```python
H = SparsePauliOp(
    ["ZZI", "IZZ", "ZIZ"],
    coeffs=[1, 1, 1]
)
```

これで：

周期スピン鎖

になります。

---

# Heisenbergモデルの実装

さらに現実的なモデル：

```text
H = X₀X₁ + Y₀Y₁ + Z₀Z₁
```

コード：

```python
H = SparsePauliOp(
    ["XX", "YY", "ZZ"],
    coeffs=[1, 1, 1]
)
```

---

# 多体系モデルの重要な応用

応用例：

磁性体解析
量子相転移
量子輸送
超伝導モデル

があります。

---

# 多体系シミュレーションの目的

私たちが知りたいもの：

基底状態
励起状態
時間発展
相関構造

です。

これらは：

Hamiltonian simulation

で求められます。

---

# 次に進むテーマ

ここまでで：

多体系時間発展

が実装できました。

次は：

より高度な方法：

**Variational simulation**

を扱います。

---

# 本章のまとめ

今回は：

* 多体系とは何か
* スピン鎖モデル
* 横磁場Isingモデル
* 周期境界条件
* Heisenbergモデル
* エンタングルメント生成
* 状態空間の指数的増大

を整理しました。

これで：

現実に近い量子系の時間発展

がシミュレーションできるようになりました。

次回は：

**Variational simulationとは何か**

を扱いながら：

NISQ時代に適した時間発展アルゴリズム

を解説します。
