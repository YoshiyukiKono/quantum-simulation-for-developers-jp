以下は **フラットな Markdown** です。そのまま
`doc/17_vqe_h2_example.md` として保存できます。

---

# doc/17_vqe_h2_example.md

# 第17回 VQEでH2分子の基底状態エネルギーを計算する

## はじめに

前回は：

* Qiskit Natureとは何か
* PySCFDriverの使い方
* 電子構造問題の生成
* Jordan–Wigner変換
* フェルミオンHamiltonianのPauli変換
* 量子ビット数の意味
* Active space / Freeze core

を整理しました。

今回は：

いよいよ

**VQEを使ってH2分子の基底状態エネルギーを計算**

します。

ここで初めて：

実際の量子化学問題

を量子アルゴリズムで解きます。

---

# VQEの流れを整理する

VQEは：

次の手順で動きます：

```text
Hamiltonianを作る
↓
Ansatzを選ぶ
↓
期待値を測定する
↓
古典最適化する
↓
エネルギー最小値を求める
```

今回は：

この流れをすべて実装します。

---

# 必要なライブラリ

まず：

ライブラリを読み込みます：

```python
from qiskit_algorithms.minimum_eigensolvers import VQE
from qiskit_algorithms.optimizers import SLSQP
from qiskit.primitives import Estimator

from qiskit.circuit.library import EfficientSU2
```

さらに：

Qiskit Nature：

```python
from qiskit_nature.second_q.drivers import PySCFDriver
from qiskit_nature.units import DistanceUnit
from qiskit_nature.second_q.mappers import JordanWignerMapper
```

---

# H2分子を定義する

まず：

分子構造を定義します：

```python
driver = PySCFDriver(
    atom="H 0 0 0; H 0 0 0.735",
    basis="sto3g",
    unit=DistanceUnit.ANGSTROM
)

problem = driver.run()
```

これで：

電子構造問題が生成されます。

---

# フェルミオンHamiltonianを取得する

次に：

Hamiltonianを取得します：

```python
fermionic_hamiltonian = problem.hamiltonian.second_q_op()
```

これは：

フェルミオン演算子形式です。

---

# 量子ビットHamiltonianへ変換する

Jordan–Wigner変換：

```python
mapper = JordanWignerMapper()

qubit_hamiltonian = mapper.map(fermionic_hamiltonian)
```

これで：

Pauli文字列の和

になります。

---

# Ansatzを選択する

今回は：

EfficientSU2

を使います：

```python
ansatz = EfficientSU2(
    num_qubits=qubit_hamiltonian.num_qubits,
    reps=1
)
```

---

# Estimatorを準備する

期待値測定器：

```python
estimator = Estimator()
```

---

# Optimizerを選択する

今回は：

SLSQP

を使います：

```python
optimizer = SLSQP(maxiter=100)
```

---

# VQEを構築する

次に：

VQEを定義します：

```python
vqe = VQE(
    estimator=estimator,
    ansatz=ansatz,
    optimizer=optimizer
)
```

---

# 固有値計算を実行する

基底状態エネルギーを求めます：

```python
result = vqe.compute_minimum_eigenvalue(
    operator=qubit_hamiltonian
)
```

---

# 計算結果を確認する

結果：

```python
print(result.eigenvalue.real)
```

これが：

H2分子の基底状態エネルギー

です。

---

# なぜこれでエネルギーが求まるのか？

VQEは：

次を最小化します：

```text
⟨ψ(θ)|H|ψ(θ)⟩
```

変分原理より：

この値は常に：

基底状態エネルギー以上

になります。

つまり：

最小値が

基底状態エネルギー

です。

---

# Ansatzの役割

Ansatzは：

探索可能な状態空間

を決めます。

今回は：

EfficientSU2

を使いましたが：

量子化学では通常：

UCC（Unitary Coupled Cluster）

が使われます。

---

# なぜEfficientSU2を使ったのか？

理由：

構造が単純
実装が簡単
教育用途に適している

ためです。

---

# UCC Ansatzとは何か？

量子化学向けAnsatz：

```text
exp(T − T†)
```

という形を持ちます。

ここで：

T = 励起演算子

です。

つまり：

電子励起構造を直接表現します。

---

# H2の結果の意味

得られるエネルギーは：

結合状態の安定性

を表します。

距離を変えると：

エネルギーが変化します。

---

# 原子間距離を変えると何が起きるか？

距離：

0.3 Å
0.5 Å
0.735 Å
1.5 Å

などで：

エネルギーを計算すると：

ポテンシャルエネルギー曲線

が得られます。

---

# ポテンシャル曲線の重要性

ポテンシャル曲線の最小点：

安定な結合距離

です。

つまり：

分子構造が決まります。

---

# 次のステップ

次は：

距離を変えながら

エネルギーを計算します：

```text
H-H距離
↓
Hamiltonian更新
↓
VQE実行
↓
エネルギー記録
```

これを繰り返します。

---

# 量子化学アルゴリズムの基本構造

まとめ：

```text
分子構造
↓
Hamiltonian生成
↓
Mapping
↓
Ansatz準備
↓
VQE
↓
基底状態エネルギー
```

です。

---

# 実務への接続

この方法は：

創薬
材料設計
触媒開発

などに応用されます。

つまり：

量子コンピュータの代表的産業応用

です。

---

# 次回への接続

次回は：

**原子間距離を変えながらVQEを実行**

して：

ポテンシャルエネルギー曲線

を描きます。

---

# 本章のまとめ

今回は：

* H2分子の定義
* Hamiltonian生成
* Jordan–Wigner変換
* EfficientSU2 Ansatz
* Estimatorの役割
* Optimizerの設定
* VQE実行
* 基底状態エネルギー取得

を整理しました。

これで：

実際の量子化学問題を

VQEで解く

ところまで到達しました。

次回は：

ポテンシャルエネルギー曲線の計算

を行います。
