以下は **フラットな Markdown** です。そのまま
`doc/16_qiskit_nature_intro.md` として保存できます。

---

# doc/16_qiskit_nature_intro.md

# 第16回 Qiskit Nature 入門 ― 分子Hamiltonianを作る

## はじめに

前回は：

* 分子Hamiltonianとは何か
* 生成演算子・消滅演算子
* Fermion-to-Qubit Mapping
* Jordan–Wigner変換
* Pauli文字列への変換
* VQE / QPEとの関係
* 量子化学アルゴリズムの全体構造

を整理しました。

今回は：

**Qiskit Nature**

を使って、

実際に分子Hamiltonianを構築します。

ここから：

理論 → 実装

へ進みます。

---

# Qiskit Natureとは何か？

Qiskit Nature は：

量子化学問題を量子回路へ変換する

ためのライブラリです。

役割：

```text
分子構造
↓
電子Hamiltonian
↓
Pauli演算子
↓
量子回路
```

を自動化します。

---

# なぜQiskit Natureが必要なのか？

電子Hamiltonianは：

非常に複雑です：

```text
H = Σ h_pq a†_p a_q
  + 1/2 Σ h_pqrs a†_p a†_q a_r a_s
```

これを手作業で変換するのは現実的ではありません。

Qiskit Nature は：

この変換を自動で行います。

---

# インストール

まず：

必要なライブラリをインストールします：

```bash
pip install qiskit-nature
pip install pyscf
```

ここで：

PySCF は量子化学計算エンジンです。

---

# 最初の分子：H2

最初に扱う分子：

水素分子 H2

です。

理由：

最も単純な分子モデルだからです。

---

# 分子構造を定義する

コード：

```python
from qiskit_nature.second_q.drivers import PySCFDriver
from qiskit_nature.units import DistanceUnit

driver = PySCFDriver(
    atom="H 0 0 0; H 0 0 0.735",
    basis="sto3g",
    unit=DistanceUnit.ANGSTROM
)
```

ここで：

```text
0.735 Å
```

は典型的な結合距離です。

---

# 電子構造問題を生成する

次に：

電子構造問題を作ります：

```python
problem = driver.run()
```

これで：

電子Hamiltonianが生成されます。

---

# Hamiltonianを取得する

電子Hamiltonian：

```python
fermionic_hamiltonian = problem.hamiltonian.second_q_op()
```

出力は：

フェルミオン演算子

です。

つまり：

まだ量子ビット表現ではありません。

---

# フェルミオンから量子ビットへ変換する

次に：

Jordan–Wigner変換を適用します：

```python
from qiskit_nature.second_q.mappers import JordanWignerMapper

mapper = JordanWignerMapper()

qubit_hamiltonian = mapper.map(fermionic_hamiltonian)
```

これで：

Pauli文字列に変換されます。

---

# 変換結果を確認する

確認：

```python
print(qubit_hamiltonian)
```

出力：

```text
0.5 * ZI
-0.3 * IZ
+0.2 * XX
...
```

のような形になります。

つまり：

これまで扱ってきた形式：

```text
H = Σ c_i P_i
```

です。

---

# 量子ビット数の確認

必要な量子ビット数：

```python
qubit_hamiltonian.num_qubits
```

H2の場合：

通常は：

4量子ビット

になります。

---

# なぜ4量子ビットなのか？

理由：

H2（STO-3G基底）では：

4つのスピン軌道

があるからです。

つまり：

1スピン軌道 = 1量子ビット

になります。

---

# さらに簡約できる

対称性を利用すると：

2量子ビット

まで削減できます。

これは：

実用的な最小モデルになります。

---

# Freeze core近似

よく使われる近似：

Freeze core

です。

意味：

内側の電子を固定する

という方法です。

これにより：

計算量が削減できます。

---

# Active spaceの考え方

もう一つ重要な概念：

Active space

です。

意味：

重要な軌道だけを選択する

という方法です。

これにより：

必要な量子ビット数を削減できます。

---

# Qiskit Natureの役割まとめ

Qiskit Nature は：

次の処理を担当します：

```text
分子構造入力
↓
電子構造計算
↓
フェルミオンHamiltonian生成
↓
Pauli演算子変換
```

つまり：

量子化学 → 量子回路

の橋渡しです。

---

# 次に何をするのか？

ここまでで：

分子Hamiltonianが作れました。

次は：

VQE

を使って：

基底状態エネルギー

を計算します。

---

# 次回への接続

次回は：

H2分子に対して：

**VQEを実行する**

ところまで進みます。

---

# 本章のまとめ

今回は：

* Qiskit Natureとは何か
* PySCFDriverの使い方
* 電子構造問題の生成
* Jordan–Wigner変換
* Pauli Hamiltonianへの変換
* 量子ビット数の意味
* Freeze core
* Active space

を整理しました。

これで：

分子Hamiltonianを量子回路へ変換する準備

が整いました。

次回は：

VQEによるH2分子エネルギー計算

を実装します。
