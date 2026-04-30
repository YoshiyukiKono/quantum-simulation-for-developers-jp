

# 第21回 量子多体系モデル ― Ising・Heisenberg・Hubbardを理解する

## はじめに

前回は：

* Active Space近似
* CAS近似
* Freeze Core
* HOMO / LUMO
* 量子ビット削減戦略
* 実機向け量子化学VQE設計

を整理しました。

ここまでで：

量子化学問題

を扱えるようになりました。

今回は：

量子多体系モデル

を扱います。

対象は：

* Isingモデル
* Heisenbergモデル
* Hubbardモデル

です。

これは：

材料科学・量子磁性・超伝導

の基礎モデルです。

---

# 量子多体系とは何か？

量子多体系とは：

多数の粒子が相互作用する系

です。

例：

```text
電子
スピン
原子格子
フォトン
```

などです。

重要なのは：

相互作用

です。

---

# なぜ量子コンピュータが必要なのか？

理由：

状態数が指数的に増えるからです。

例：

| スピン数 | 状態数   |
| ---- | ----- |
| 5    | 32    |
| 10   | 1024  |
| 50   | 約10¹⁵ |

古典計算では：

急速に扱えなくなります。

ここで：

量子コンピュータ

が活躍します。

---

# Isingモデルとは何か？

最も単純な量子多体系モデル：

Isingモデル

です。

Hamiltonian：

```text
H = -J Σ Zi Zj - h Σ Xi
```

意味：

| 記号   | 意味      |
| ---- | ------- |
| J    | 相互作用強度  |
| h    | 外部磁場    |
| ZiZj | スピン相互作用 |
| Xi   | 横磁場     |

です。

---

# Isingモデルの直感的理解

スピンは：

```text
↑
↓
```

の2状態を持ちます。

隣接スピンが：

揃うか

反対になるか

でエネルギーが変わります。

---

# なぜ重要なのか？

Isingモデルは：

相転移

を記述できます。

例：

```text
磁性
秩序状態
臨界現象
```

です。

---

# QiskitでIsingモデルを作る

例：

```python
from qiskit.quantum_info import SparsePauliOp

hamiltonian = SparsePauliOp.from_list([
    ("ZZ", -1.0),
    ("XI", -0.5),
    ("IX", -0.5)
])
```

これで：

2スピンIsingモデル

が構築できます。

---

# Heisenbergモデルとは何か？

Isingモデルの拡張：

Heisenbergモデル

です。

Hamiltonian：

```text
H = J Σ (XiXj + YiYj + ZiZj)
```

特徴：

3方向すべての相互作用

を扱います。

---

# Isingモデルとの違い

比較：

| モデル        | 相互作用     |
| ---------- | -------- |
| Ising      | Z方向のみ    |
| Heisenberg | X,Y,Zすべて |

つまり：

より現実的な磁性モデル

です。

---

# Heisenbergモデルの応用

応用例：

```text
量子磁性
スピン鎖
量子相転移
量子情報伝搬
```

です。

---

# QiskitでHeisenbergモデルを作る

例：

```python
hamiltonian = SparsePauliOp.from_list([
    ("XX", 1.0),
    ("YY", 1.0),
    ("ZZ", 1.0)
])
```

これで：

2スピンHeisenbergモデル

になります。

---

# Hubbardモデルとは何か？

電子相関を扱う代表モデル：

Hubbardモデル

です。

Hamiltonian：

```text
H = -t Σ (c†i cj + h.c.) + U Σ ni↑ ni↓
```

意味：

| 記号 | 意味     |
| -- | ------ |
| t  | ホッピング  |
| U  | クーロン反発 |
| ni | 電子数    |

です。

---

# Hubbardモデルの直感的理解

電子は：

サイト間を移動します：

```text
i → j
```

同じサイトに2電子が入ると：

エネルギーが増えます。

これが：

電子相関

です。

---

# なぜ重要なのか？

Hubbardモデルは：

次を説明できます：

```text
金属
絶縁体
磁性
高温超伝導
```

つまり：

材料科学の核心モデル

です。

---

# Qiskit NatureでHubbardモデルを作る

例：

```python
from qiskit_nature.second_q.hamiltonians.lattices import LineLattice
from qiskit_nature.second_q.hamiltonians import FermiHubbardModel

lattice = LineLattice(num_nodes=2)

model = FermiHubbardModel(
    lattice=lattice,
    onsite_interaction=4.0,
    hopping_parameter=1.0
)
```

これで：

Hubbard Hamiltonian

が生成されます。

---

# なぜ量子アルゴリズムと相性が良いのか？

理由：

自然にPauli演算子へ変換できるからです。

つまり：

```text
Fermion
↓
Qubit
↓
Pauli operator
```

という流れが成立します。

---

# VQEとの組み合わせ

量子多体系モデルは：

VQE

と非常に相性が良いです。

理由：

基底状態エネルギー

を求めたいからです。

---

# QAOAとの関係

Isingモデルは：

QAOA

の基本構造です。

つまり：

組合せ最適化問題

にもつながります。

---

# 量子化学との違い

比較：

| 分野    | 対象    |
| ----- | ----- |
| 量子化学  | 分子    |
| 多体系物理 | 格子モデル |

どちらも：

Hamiltonian固有値問題

です。

---

# 実機で扱いやすいモデル

NISQ向きモデル：

```text
Ising
Heisenberg
小規模Hubbard
```

理由：

回路が比較的浅いからです。

---

# 材料科学への応用

応用例：

```text
磁性体
半導体
超伝導体
トポロジカル物質
```

です。

ここから：

量子材料シミュレーション

へ進めます。

---

# ここまでで何ができるようになったか？

できるようになったこと：

* Isingモデル理解
* Heisenbergモデル理解
* Hubbardモデル理解
* 格子Hamiltonian構築
* Pauli演算子変換
* VQE適用

つまり：

量子材料モデル

が扱えるようになりました。

---

# 次回への接続

次回は：

**量子位相とトポロジカル物質**

を扱います。

ここから：

現代量子物性

へ進みます。

---

# 本章のまとめ

今回は：

* 量子多体系とは何か
* Isingモデル
* Heisenbergモデル
* Hubbardモデル
* 格子Hamiltonian
* 材料科学への応用
* VQEとの関係
* QAOAとの関係

を整理しました。

これで：

量子化学だけでなく

量子材料モデル

も扱えるようになりました。
