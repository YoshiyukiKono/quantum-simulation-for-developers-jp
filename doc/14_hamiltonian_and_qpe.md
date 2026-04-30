以下は **フラットな Markdown** です。そのまま
`doc/14_hamiltonian_and_qpe.md` として保存できます。

---

# doc/14_hamiltonian_and_qpe.md

# 第14回 Hamiltonian simulation と QPE の統合

## はじめに

前回は：

* QPEの目的
* 位相と固有値の関係
* 制御付きユニタリ演算の役割
* 逆QFTの意味
* 精度スケーリング
* VQEとの違い
* Shorアルゴリズムとの関係

を整理しました。

今回は：

これまで学んできた

Hamiltonian simulation
Variational simulation
VQE
QPE

を統合して：

**量子アルゴリズムの全体構造**

を完成させます。

---

# QPEは単独では動かない

重要な事実：

QPEは単体のアルゴリズムではありません。

必要なのは：

時間発展演算子です。

つまり：

genui{"math_block_widget_always_prefetch_v2": {"content": "U = e^{-iHt}"}}

$$ U = e^{-iHt} $$

を実装する必要があります。

ここで登場するのが：

Hamiltonian simulation

です。

---

# ここまでの流れを整理する

これまでのシリーズの構造：

| 技術                     | 役割        |
| ---------------------- | --------- |
| Trotter分解              | 時間発展の近似   |
| Variational simulation | 浅い回路で時間発展 |
| VQE                    | 最小固有値探索   |
| QPE                    | 任意固有値推定   |

つまり：

すべてが：

Hamiltonian

に依存しています。

---

# QPEの内部構造

QPEの核心：

制御付き時間発展演算子：

```text
CU
CU²
CU⁴
CU⁸
...
```

です。

ここで：

```text
U = exp(-iHt)
```

です。

つまり：

Hamiltonian simulation がなければ

QPEは実装できません。

---

# なぜ指数的に冪乗するのか？

理由：

位相を

ビット単位で読み取るため

です。

例えば：

```text
CU²
```

は：

位相を2倍にします。

```text
CU⁴
```

は：

位相を4倍にします。

これにより：

2進表現が取得できます。

---

# QPEの実装に必要なもの

QPEに必要な要素：

① 固有状態準備
② Hamiltonian simulation
③ 制御付きユニタリ
④ 逆QFT

です。

このうち：

最も難しいのが

Hamiltonian simulation

です。

---

# 固有状態準備の問題

実は：

QPEの最大の課題は：

固有状態準備

です。

なぜなら：

QPEは：

固有状態を入力として仮定

するからです。

---

# 固有状態が準備できない場合

この問題の解決策：

VQE

です。

流れ：

VQEで近似基底状態を準備

↓

QPEで高精度固有値推定

という構造になります。

---

# VQE + QPE の統合構造

これは：

FTQC時代の標準構成です。

流れ：

```text
VQE → 固有状態準備
↓
Hamiltonian simulation
↓
QPE → 高精度固有値取得
```

つまり：

NISQ と FTQC の接続

です。

---

# Variational simulationの役割

Variational simulation は：

浅い回路で

時間発展を近似

する方法でした。

これは：

NISQ向きです。

つまり：

Trotter分解の代替手段

として使えます。

---

# 時代別アルゴリズム構造

整理すると：

NISQ時代：

```text
VQE
VarQITE
VarQRTE
```

FTQC時代：

```text
Hamiltonian simulation
+
QPE
```

という構造になります。

---

# なぜHamiltonian simulationが中心なのか？

理由：

量子力学の基本方程式が：

```text
H|ψ⟩ = E|ψ⟩
```

だからです。

つまり：

Hamiltonian を扱えることが

量子アルゴリズムの本質

です。

---

# 量子化学アルゴリズムの完成形

量子化学では：

次の流れになります：

① 分子Hamiltonian構築
② VQEで近似基底状態
③ Hamiltonian simulation
④ QPEで高精度固有値

これが：

実用量子化学アルゴリズム

です。

---

# 材料科学でも同じ構造

材料科学でも：

同様です：

① モデルHamiltonian構築
② 初期状態準備
③ 時間発展演算
④ 固有値取得

つまり：

応用分野が違うだけで

構造は同じです。

---

# Shorアルゴリズムとの関係

Shorアルゴリズムも：

内部構造は：

QPE

です。

つまり：

素因数分解も：

位相推定問題

です。

---

# Hamiltonian simulationの重要性

ここまで見てきたように：

Hamiltonian simulation は：

すべての基盤

です。

役割：

時間発展
固有値取得
量子ダイナミクス
材料解析
分子解析

すべてを支えます。

---

# 量子アルゴリズムの統一構造

ここで：

シリーズ全体の構造を整理できます：

```text
Hamiltonian
↓
時間発展 exp(-iHt)
↓
位相変化
↓
固有値取得
```

これが：

量子アルゴリズムの中心構造

です。

---

# なぜこの理解が重要なのか？

理由：

個別アルゴリズムを

暗記する必要がなくなる

からです。

すべて：

Hamiltonian問題

として理解できます。

---

# 開発者視点での意味

開発者にとって重要なのは：

次の理解です：

量子アルゴリズムを書く

＝

Hamiltonianを書く

ということです。

---

# 実装レベルでの統一構造

Qiskitでは：

すべて：

次の流れになります：

```python
H = SparsePauliOp(...)
evolution = PauliEvolutionGate(H, time=t)
```

つまり：

Hamiltonian を中心に

すべてが構成されます。

---

# ここまでで何ができるようになったのか？

このシリーズの到達点：

* Hamiltonianを理解できる
* 時間発展を実装できる
* Variational simulationを扱える
* VQEを理解できる
* QPEを理解できる
* 固有値問題を扱える

つまり：

量子アルゴリズムの中核構造

が理解できました。

---

# 次に進むテーマ

次回は：

**量子化学への応用**

を扱います。

ここから：

実際の応用分野

へ入っていきます。

---

# 本章のまとめ

今回は：

* QPEとHamiltonian simulationの関係
* 制御付き時間発展演算の役割
* 固有状態準備の問題
* VQEとの統合構造
* NISQとFTQCの接続
* 量子化学アルゴリズムの構造
* 量子アルゴリズムの統一理解

を整理しました。

これで：

量子アルゴリズムの全体構造

が完成しました。

次回は：

量子化学への応用として：

分子Hamiltonianの扱い方

を解説します。
