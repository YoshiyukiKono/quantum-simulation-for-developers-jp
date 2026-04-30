以下は **フラットな Markdown** です。そのまま
`doc/09_variational_simulation.md` として保存できます。

---

# doc/09_variational_simulation.md

# 第09回 Variational simulationとは何か

## はじめに

前回は：

* 多体系とは何か
* スピン鎖モデル
* 横磁場Isingモデル
* 周期境界条件
* Heisenbergモデル
* エンタングルメント生成
* 多体系時間発展の実装

を整理しました。

ここまでで：

Trotter分解による

時間発展シミュレーション

が実装できるようになりました。

しかし：

重要な問題があります。

それは：

**回路が長くなりすぎる**

という問題です。

この問題を解決する方法が：

**Variational simulation**

です。

---

# なぜVariational simulationが必要なのか？

Trotter分解では：

時間を細かく分割する必要があります：

```text
U(t) = (exp(-iHΔt))^n
```

すると：

Δt を小さくするほど

精度が上がります。

しかし：

回路が長くなります。

---

# NISQ時代の制約

現在の量子コンピュータには：

制約があります：

量子ビット数が少ない
回路深さが制限される
ノイズが大きい

つまり：

長い回路が実行できません。

---

# Variational simulationの基本アイデア

Variational simulationでは：

次のように考えます：

正確な時間発展：

```text
|ψ(t)⟩ = exp(-iHt)|ψ(0)⟩
```

を：

近似回路で表現します：

```text
|ψ(θ(t))⟩
```

つまり：

時間発展を

パラメータ付き回路

で近似します。

---

# パラメータ付き回路とは何か？

例：

```text
|ψ(θ)⟩ = U(θ)|ψ(0)⟩
```

ここで：

θ は調整可能なパラメータです。

時間発展に合わせて：

θ(t)

を更新します。

---

# Variational simulationの目標

目標：

次を満たすこと：

```text
|ψ(θ(t))⟩ ≈ exp(-iHt)|ψ(0)⟩
```

つまり：

時間発展を近似する

ことです。

---

# なぜこれが有効なのか？

理由：

回路の長さを固定できる

からです。

つまり：

深い回路を使わずに

時間発展を近似できます。

---

# Variational principleとは何か？

Variational simulationの基盤：

変分原理

です。

基本アイデア：

誤差が最小になるように

パラメータを更新する

という方法です。

---

# McLachlan変分原理

代表的な方法：

McLachlan variational principle

です。

式：

```text
δ || (d/dt)|ψ(θ)⟩ + iH|ψ(θ)⟩ || = 0
```

意味：

時間発展との差を最小化する

という条件です。

---

# 直感的な意味

本来の時間発展：

```text
d|ψ⟩/dt = -iH|ψ⟩
```

近似時間発展：

```text
d|ψ(θ)⟩/dt
```

この差を：

最小にします。

---

# 何が起きているのか？

Variational simulationでは：

量子回路の形は固定

します。

変えるのは：

パラメータだけ

です。

---

# なぜ回路を固定するのか？

理由：

回路が深くならない

からです。

つまり：

NISQ向きの方法

になります。

---

# パラメータ更新の仕組み

更新ルール：

```text
A(θ) θ̇ = C(θ)
```

ここで：

A = 行列
C = ベクトル

です。

これを解くことで：

θ(t)

が更新されます。

---

# 行列Aとは何か？

定義：

```text
Aᵢⱼ = Re(⟨∂ᵢψ | ∂ⱼψ⟩)
```

つまり：

状態の変化の重なり

です。

---

# ベクトルCとは何か？

定義：

```text
Cᵢ = Im(⟨∂ᵢψ | H | ψ⟩)
```

つまり：

Hamiltonianとの関係

を表します。

---

# Variational simulationの流れ

基本手順：

① Ansatzを選ぶ
② 初期状態を準備
③ A行列を計算
④ Cベクトルを計算
⑤ θ を更新

これを繰り返します。

---

# Ansatzとは何か？

Ansatzとは：

状態を表現する回路構造

です。

例：

```text
RX(θ₁)
RY(θ₂)
CNOT
```

などです。

---

# なぜAnsatzが重要なのか？

理由：

表現能力が決まる

からです。

つまり：

Ansatzが悪いと

時間発展を近似できません。

---

# Trotter法との違い

比較：

Trotter法：

回路が長くなる

Variational法：

回路は固定

つまり：

ノイズに強い

という利点があります。

---

# Variational simulationの種類

代表例：

Real-time evolution
Imaginary-time evolution

があります。

---

# Real-time evolutionとは何か？

通常の時間発展：

```text
exp(-iHt)
```

を近似します。

用途：

量子ダイナミクス解析

です。

---

# Imaginary-time evolutionとは何か？

次の形：

```text
exp(-Hτ)
```

を使います。

これは：

基底状態探索

に使われます。

---

# なぜ基底状態が見つかるのか？

理由：

高エネルギー状態が

指数的に減衰する

からです。

つまり：

最終的に

最低エネルギー状態

が残ります。

---

# VQEとの関係

Imaginary-time evolutionは：

VQEと密接に関係しています。

つまり：

VQE = 変分基底状態探索

です。

---

# Variational simulationのメリット

利点：

回路が浅い
ノイズに強い
NISQ向き
拡張性が高い

です。

---

# Variational simulationの課題

課題：

Ansatz設計
数値安定性
古典計算コスト

があります。

---

# どんな場面で使うのか？

用途：

量子ダイナミクス
分子シミュレーション
基底状態探索
量子多体系解析

です。

---

# Qiskitとの関係

Qiskitでは：

Variational simulationは：

VQE
VarQITE
VarQRTE

として実装されています。

---

# 次に進むテーマ

次回は：

**Variational time evolution の実装**

を扱いながら：

実際にQiskitで

変分時間発展を動かしてみます。

---

# 本章のまとめ

今回は：

* Variational simulationの基本概念
* McLachlan変分原理
* Ansatzの役割
* パラメータ更新の仕組み
* Real-time evolution
* Imaginary-time evolution
* VQEとの関係

を整理しました。

これで：

NISQ時代に適した

時間発展アルゴリズム

の基本が理解できました。

次回は：

Qiskitを使った

Variational time evolution の実装

を解説します。
