以下は **フラットな Markdown** です。そのまま
`doc/20_active_space.md` として保存できます。

---

# doc/20_active_space.md

# 第20回 Active Space近似 ― 量子ビット数を減らす実践技術

## はじめに

前回は：

* Coupled Cluster法とは何か
* Unitary Coupled Clusterの意味
* Single / Double excitation
* UCCSD Ansatzの構造
* Hartree–Fock初期状態
* EfficientSU2との違い
* 量子化学VQEにおけるAnsatz設計

を整理しました。

今回は：

量子化学計算で非常に重要な技術：

**Active Space近似**

を扱います。

これは：

量子ビット数を削減するための核心技術

です。

---

# なぜ量子ビット削減が必要なのか？

量子化学問題では：

軌道数 = 量子ビット数

になります。

つまり：

軌道が増えるほど

量子ビットが増えます。

例：

| 分子   | 必要量子ビット数 |
| ---- | -------- |
| H₂   | 4        |
| LiH  | 12       |
| BeH₂ | 14       |
| H₂O  | 20以上     |

となります。

現在のNISQ環境では：

これは大きすぎます。

---

# Active Spaceとは何か？

Active Spaceとは：

重要な軌道だけ選択する方法

です。

つまり：

```text
全軌道
↓
重要な軌道だけ選択
↓
量子計算に使用
```

という処理を行います。

---

# なぜすべての軌道が必要ではないのか？

理由：

電子の多くは：

化学反応に関与しない

からです。

特に：

内殻電子（core electrons）

は影響が小さいです。

---

# 軌道の種類

電子軌道は通常：

3種類に分かれます：

```text
Core orbitals
Active orbitals
Virtual orbitals
```

それぞれ：

| 軌道      | 役割      |
| ------- | ------- |
| Core    | 常に占有    |
| Active  | 化学反応に関与 |
| Virtual | 通常は空    |

です。

---

# Active Space近似の考え方

基本構造：

```text
Core → 固定
Active → 量子計算
Virtual → 無視または近似
```

これにより：

量子ビット数を削減できます。

---

# Freeze Core近似との違い

Freeze Core：

内殻電子を固定

Active Space：

重要軌道のみ選択

つまり：

Active Spaceの方が柔軟です。

---

# Active Spaceの典型例

例：

CAS(2,2)

意味：

```text
2 electrons
2 orbitals
```

のみ扱うという意味です。

これは：

最も基本的なActive Spaceです。

---

# CASとは何か？

CAS：

Complete Active Space

の略です。

意味：

選択した軌道内で

すべての電子配置を考慮する

という方法です。

---

# CAS(2,2)の直感的理解

例：

2電子
2軌道

なら：

電子の配置は：

```text
↑↑
↑↓
↓↑
↓↓
```

すべて考慮します。

つまり：

完全な量子状態空間

を扱います。

---

# Qiskit NatureでActive Spaceを使う

Qiskit Natureでは：

ActiveSpaceTransformer

を使います：

```python
from qiskit_nature.second_q.transformers import ActiveSpaceTransformer
```

---

# Active Spaceを設定する

例：

```python
transformer = ActiveSpaceTransformer(
    num_electrons=2,
    num_spatial_orbitals=2
)

reduced_problem = transformer.transform(problem)
```

これで：

Active Space Hamiltonian

が生成されます。

---

# 変換後のHamiltonian

取得：

```python
fermionic_hamiltonian = reduced_problem.hamiltonian.second_q_op()
```

これを：

Jordan–Wigner変換します：

```python
mapper = JordanWignerMapper()
qubit_hamiltonian = mapper.map(fermionic_hamiltonian)
```

---

# 量子ビット削減の効果

例：

H₂（通常）：

```text
4 qubits
```

CAS(2,2)：

```text
2 qubits
```

になります。

つまり：

回路サイズが半分になります ✨

---

# なぜ精度が維持できるのか？

理由：

重要な電子相関

だけを残しているからです。

つまり：

不要な自由度を削除しています。

---

# Active Space選択の難しさ

問題：

どの軌道が重要か？

を決める必要があります。

これは：

量子化学の知識

が必要になります。

---

# 一般的な選択方法

代表例：

HOMO
LUMO

周辺の軌道を選びます。

つまり：

```text
HOMO−1
HOMO
LUMO
LUMO+1
```

などです。

---

# HOMOとLUMOとは何か？

HOMO：

Highest Occupied Molecular Orbital

LUMO：

Lowest Unoccupied Molecular Orbital

です。

化学反応は：

この近傍で起こります。

---

# Active SpaceとUCCSDの関係

Active Spaceを選択すると：

UCCSD回路が

大幅に小さくなります。

つまり：

実機実行が現実的になります。

---

# Active Spaceの設計戦略

基本方針：

```text
Core → Freeze
Valence → Active
High virtual → Ignore
```

これが：

標準戦略です。

---

# 実用量子化学での役割

実際の量子化学VQEでは：

必ず

Active Space

を使います。

理由：

量子ビットが不足しているからです。

---

# Active Spaceと誤り訂正量子計算

将来：

誤り訂正量子コンピュータ

が実現すると：

Active Spaceは縮小できます。

しかし：

完全には不要になりません。

なぜなら：

計算効率の問題

が残るからです。

---

# Active Spaceの直感的理解

Active Spaceとは：

問題の重要部分だけ解く

という方法です。

つまり：

```text
重要部分 → 量子計算
それ以外 → 古典計算
```

という役割分担です。

---

# ここまでで何ができるようになったか？

できるようになったこと：

* 分子Hamiltonian生成
* Pauli変換
* VQE実装
* UCCSD Ansatz使用
* Active Space選択
* 量子ビット削減

つまり：

実用レベルの量子化学VQE設計

が可能になりました 🧪

---

# 次回への接続

次回は：

**量子多体系シミュレーション**

へ進みます。

ここから：

量子化学

↓

材料科学

への拡張が始まります。

---

# 本章のまとめ

今回は：

* Active Spaceとは何か
* CAS近似
* Freeze Coreとの違い
* HOMO / LUMOの意味
* Qiskit Natureでの実装方法
* 量子ビット削減効果
* UCCSDとの関係
* 実用量子化学での役割

を整理しました。

これで：

NISQ環境で現実的に動く量子化学アルゴリズム

が構築できるようになりました。

次回は：

量子多体系モデル

（Ising / Heisenberg / Hubbard）

を扱います。
