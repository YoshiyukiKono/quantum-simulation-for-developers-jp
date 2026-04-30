以下は **フラットな Markdown** です。そのまま
`doc/06_trotter_decomposition.md` として保存できます。

---

# doc/06_trotter_decomposition.md

# 第06回 Trotter分解

## はじめに

前回は：

* 量子回路と時間発展の関係
* 回転ゲートと時間発展演算子
* 相互作用Hamiltonianの回路表現
* 非可換性の問題
* デジタル量子シミュレーションの基本構造

を整理しました。

ここで明らかになった重要な問題は：

```text
exp(A + B) ≠ exp(A)exp(B)
```

という関係です。

つまり：

複数項のHamiltonianは

そのままでは回路に変換できません。

この問題を解決する方法が：

**Trotter分解（Suzuki–Trotter分解）**

です。

---

# Trotter分解とは何か？

Trotter分解とは：

指数関数の和を

指数関数の積として近似する方法

です。

つまり：

```text
exp((A + B)t)
```

を：

```text
(exp(AΔt) exp(BΔt))^n
```

として近似します。

ここで：

```text
t = nΔt
```

です。

---

# なぜこの近似が成立するのか？

重要な条件：

Δt が十分小さい

とき：

```text
exp((A + B)Δt) ≈ exp(AΔt)exp(BΔt)
```

が成立します。

そして：

これを繰り返すことで：

長時間の時間発展を近似できます。

---

# Hamiltonian simulationへの応用

Hamiltonianが：

```text
H = A + B
```

のとき：

時間発展演算子：

```text
exp(-iHt)
```

は：

次のように近似できます：

```text
(exp(-iAΔt) exp(-iBΔt))^n
```

これが：

Trotter分解です。

---

# より一般的な場合

実際のHamiltonianは：

通常：

複数項の和です：

```text
H = Σ cᵢPᵢ
```

このとき：

時間発展演算子：

```text
exp(-iHt)
```

は：

次のように近似できます：

```text
(∏ exp(-i cᵢPᵢ Δt))^n
```

これにより：

回路として実装可能になります。

---

# 1次Trotter分解

最も基本的な形：

**1次Trotter分解**

です。

式：

```text
exp((A + B)t) ≈ (exp(AΔt) exp(BΔt))^n
```

特徴：

実装が簡単
誤差が比較的大きい

という性質があります。

---

# 誤差はどこから生まれるのか？

誤差の原因：

A と B が非可換

だからです。

つまり：

```text
[A, B] ≠ 0
```

のとき：

近似誤差が発生します。

---

# 可換な場合はどうなるか？

もし：

```text
[A, B] = 0
```

なら：

次が成立します：

```text
exp(A + B) = exp(A)exp(B)
```

つまり：

Trotter分解は不要です。

---

# 高次Trotter分解

精度を上げる方法：

**高次Trotter分解**

です。

例：

2次Trotter分解：

```text
exp((A + B)t) ≈ (exp(AΔt/2) exp(BΔt) exp(AΔt/2))^n
```

特徴：

誤差が小さい
回路が少し長くなる

というトレードオフがあります。

---

# Suzuki–Trotter展開

さらに一般化された形：

**Suzuki–Trotter展開**

です。

これは：

任意の次数で近似可能

です。

つまり：

精度を自由に調整できます。

---

# なぜTrotter分解が重要なのか？

理由：

Hamiltonian simulationの基本アルゴリズム

だからです。

量子シミュレーションの多くは：

Trotter分解を基盤にしています。

---

# 実際の例で考える

例：

```text
H = X + Z
```

時間発展：

```text
exp(-i(X + Z)t)
```

は：

次のように近似できます：

```text
(exp(-iXΔt) exp(-iZΔt))^n
```

ここで：

```text
t = nΔt
```

です。

---

# 回路としての実装

それぞれの項：

```text
exp(-iXΔt)
```

は：

```text
RX(2Δt)
```

として実装できます。

同様に：

```text
exp(-iZΔt)
```

は：

```text
RZ(2Δt)
```

になります。

つまり：

回転ゲートの列

として実装できます。

---

# 相互作用項の例

例：

```text
H = Z₀Z₁
```

なら：

```text
exp(-iZ₀Z₁Δt)
```

は：

```text
CNOT
RZ
CNOT
```

で実装できます。

---

# 多項Hamiltonianの場合

例：

```text
H = X₀ + Z₀Z₁ + X₁
```

なら：

次のように分解できます：

```text
exp(-iX₀Δt)
exp(-iZ₀Z₁Δt)
exp(-iX₁Δt)
```

これを：

n 回繰り返します。

---

# Trotterステップとは何か？

1回の近似操作：

```text
exp(-iAΔt) exp(-iBΔt)
```

を：

**Trotterステップ**

と呼びます。

つまり：

回路の基本ブロックです。

---

# Trotterステップ数と精度の関係

重要な関係：

ステップ数が多い

↓

精度が高い

しかし：

回路が長くなる

という問題があります。

---

# 回路深さとのトレードオフ

まとめると：

Trotterステップ数 ↑

↓

精度 ↑
回路深さ ↑
ノイズ影響 ↑

という関係になります。

---

# NISQ時代の現実的戦略

現在の量子デバイスでは：

回路が短い方が有利

です。

そのため：

少ないTrotterステップ

が使われます。

つまり：

精度と実装可能性のバランス

が重要です。

---

# QiskitでのTrotter分解

Qiskitでは：

次のようなツールが使えます：

PauliEvolutionGate
TrotterQRTE
SuzukiTrotter

これにより：

自動的に回路生成できます。

---

# Hamiltonian simulationの基本構造

ここまでをまとめると：

量子シミュレーションは：

次の流れになります：

HamiltonianをPauli分解
↓
時間発展演算子を構成
↓
Trotter分解で近似
↓
回路として実装

これが：

基本アルゴリズムです。

---

# 次に進むテーマ

ここまでで：

理論的準備

が整いました。

次はいよいよ：

**Qiskitで時間発展を実装する方法**

を扱います。

---

# 本章のまとめ

今回は：

* Trotter分解とは何か
* 非可換性の問題の解決方法
* 1次Trotter分解
* 高次Trotter分解
* Suzuki–Trotter展開
* 回路深さとのトレードオフ
* 実装戦略

を整理しました。

これで：

複雑なHamiltonianを

量子回路として実装する方法

が理解できました。

次回は：

**Qiskitでの時間発展シミュレーション**

を実装しながら：

実際にHamiltonian simulationを動かしてみます。
