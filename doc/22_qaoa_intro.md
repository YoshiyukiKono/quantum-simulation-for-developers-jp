以下は **フラットな Markdown** です。そのまま
`doc/22_qaoa_intro.md` として保存できます。

---

# doc/22_qaoa_intro.md

# 第22回 QAOAとは何か ― 組合せ最適化のための量子アルゴリズム

## はじめに

前回は：

* Isingモデル
* Heisenbergモデル
* Hubbardモデル
* 格子Hamiltonian
* 多体系物理モデルとVQEの関係

を整理しました。

今回は：

NISQ時代を代表する量子アルゴリズム：

**QAOA（Quantum Approximate Optimization Algorithm）**

を扱います。

これは：

組合せ最適化問題

を解くための量子アルゴリズムです。

---

# なぜ最適化問題が重要なのか？

現実の多くの問題は：

最適化問題

として表現できます。

例：

```text
配送ルート最適化
スケジューリング
ネットワーク設計
ポートフォリオ最適化
回路配置問題
```

これらは：

NP困難問題

であることが多いです。

---

# QAOAとは何か？

QAOAとは：

パラメータ付き量子回路

を用いて：

最適解に近い解

を探索するアルゴリズムです。

基本構造：

```text
初期状態
↓
Cost Hamiltonian
↓
Mixer Hamiltonian
↓
測定
↓
古典最適化
```

という流れになります。

---

# VQEとの関係

QAOAは：

VQEの特殊な形式

と考えることができます。

比較：

| アルゴリズム | 目的     |
| ------ | ------ |
| VQE    | 基底状態探索 |
| QAOA   | 最適解探索  |

どちらも：

パラメータ最適化

を行います。

---

# QAOAの基本アイデア

最適化問題を：

Hamiltonian

として表現します。

そして：

その基底状態

を探します。

つまり：

```text
最適化問題
↓
Hamiltonian
↓
基底状態探索
```

になります。

---

# Cost Hamiltonianとは何か？

Cost Hamiltonianとは：

最適化したい目的関数

を表現するHamiltonianです。

例：

MaxCut問題なら：

```text
H = Σ Zij
```

の形になります。

---

# Mixer Hamiltonianとは何か？

Mixer Hamiltonianとは：

探索空間を広げるための操作

です。

典型例：

```text
Σ Xi
```

です。

役割：

状態の探索

を可能にします。

---

# QAOA回路の構造

QAOA回路は：

次の形になります：

```text
|ψ⟩ = U_M(β) U_C(γ) |+⟩
```

ここで：

| 記号  | 意味              |
| --- | --------------- |
| U_C | Cost evolution  |
| U_M | Mixer evolution |
| γ   | costパラメータ       |
| β   | mixerパラメータ      |

です。

---

# なぜ|+⟩状態から始めるのか？

理由：

探索空間全体を均等に含む

からです。

つまり：

```text
|+⟩ = (|0⟩ + |1⟩)/√2
```

は：

すべての候補解の重ね合わせ

です。

---

# QAOAの数式構造

QAOA状態は：

次の形になります：

```text
|ψ(γ, β)⟩ = U_M(β) U_C(γ) |+⟩
```

これを：

繰り返し適用します：

```text
(U_M U_C)^p
```

ここで：

p = レイヤー数

です。

---

# レイヤー数pの意味

pが大きいほど：

精度が向上します。

つまり：

```text
p = 1 → 粗い近似
p = 5 → より高精度
p → ∞ → 最適解
```

になります。

---

# QAOAの処理フロー

処理の流れ：

```text
1 問題をHamiltonian化
2 初期状態準備
3 Cost evolution
4 Mixer evolution
5 測定
6 古典最適化
7 繰り返し
```

です。

---

# QiskitでQAOAを使う

Qiskitでは：

QAOAクラス

が用意されています：

```python
from qiskit_algorithms import QAOA
```

---

# Optimizerの設定

例：

```python
from qiskit_algorithms.optimizers import COBYLA

optimizer = COBYLA()
```

---

# QAOAインスタンスの作成

例：

```python
qaoa = QAOA(
    sampler=sampler,
    optimizer=optimizer,
    reps=1
)
```

ここで：

reps = p

です。

---

# なぜIsingモデルが重要なのか？

理由：

多くの最適化問題は：

Isingモデル

に変換できるからです。

つまり：

```text
最適化問題
↓
Isingモデル
↓
QAOA
```

という流れになります。

---

# QAOAの直感的理解

QAOAは：

2つの操作を交互に適用します：

```text
Cost evolution → 解を良くする
Mixer evolution → 解を探索する
```

つまり：

探索と改善

を繰り返しています。

---

# QAOAはなぜNISQ向きなのか？

理由：

回路が浅いからです。

つまり：

少ない量子ビット

短い回路深さ

で動作します。

---

# VQEとの違い（重要）

比較：

| 特徴          | VQE  | QAOA  |
| ----------- | ---- | ----- |
| Ansatz      | 任意   | 構造化   |
| 目的          | 基底状態 | 最適解   |
| Hamiltonian | 物理系  | 最適化問題 |

です。

---

# QAOAの応用分野

代表例：

```text
MaxCut
巡回セールスマン問題
グラフ彩色
スケジューリング
ポートフォリオ最適化
```

です。

---

# NISQ時代における位置づけ

現在：

QAOAは：

最も研究されている

量子最適化アルゴリズムです。

理由：

実機で実装可能だからです。

---

# ここまでで何ができるようになったか？

できるようになったこと：

* 最適化問題の量子表現
* Cost Hamiltonian理解
* Mixer Hamiltonian理解
* レイヤー構造理解
* VQEとの違い理解

つまり：

量子最適化アルゴリズム

の入口に立ちました。

---

# 次回への接続

次回は：

**MaxCut問題をQAOAで解く**

実装を行います。

ここで：

実際に最適化問題を量子回路として構築します。

---

# 本章のまとめ

今回は：

* QAOAとは何か
* Cost Hamiltonian
* Mixer Hamiltonian
* レイヤー構造
* Isingモデルとの関係
* VQEとの違い
* NISQでの役割

を整理しました。

次回は：

具体例として

MaxCut問題

を量子回路で実装します。
