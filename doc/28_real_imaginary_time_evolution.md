以下は **フラットな Markdown** です。そのまま
`doc/28_real_imaginary_time_evolution.md` として保存できます。

---

# doc/28_real_imaginary_time_evolution.md

# 第28回 Real-time / Imaginary-time evolution ― 時間発展アルゴリズムの実践応用

## はじめに

前回は：

* Hamiltonian Simulation
* Trotter分解
* Pauli指数演算
* ZZ相互作用回路
* PauliEvolutionGate
* repsパラメータ
* 精度と回路深さのトレードオフ

を整理しました。

今回は：

時間発展アルゴリズムの2つの重要な形：

* Real-time evolution
* Imaginary-time evolution

を扱います。

ここで：

時間発展と基底状態探索

が統一的に理解できるようになります ✨

---

# 時間発展には2種類ある

量子計算では：

2種類の時間発展があります：

```text
Real-time evolution
Imaginary-time evolution
```

それぞれ：

まったく異なる目的

を持っています。

---

# Real-time evolutionとは何か？

Real-time evolutionは：

物理的な時間変化

を再現する操作です。

時間発展演算子：

genui{"math_block_widget_always_prefetch_v2": {"content": "U(t) = e^{-iHt}"} }

これは：

Schrödinger方程式の解

でした。

---

# Real-time evolutionの意味

この操作は：

量子状態の回転

を表します。

つまり：

```text
状態の保存（ノルム保存）
+
位相の変化
```

が起こります。

これは：

ユニタリ変換

です。

---

# Real-time evolutionの応用

代表例：

```text
量子ダイナミクス
波束伝播
量子ウォーク
スピン伝播
量子材料シミュレーション
```

です。

つまり：

自然現象の再現

に使います 🌍

---

# 固有状態のReal-time evolution

もし状態が：

Hamiltonianの固有状態なら：

```text
H|ψ⟩ = E|ψ⟩
```

時間発展は：

```text
位相が変わるだけ
```

になります。

つまり：

物理状態は変わりません。

---

# 一般状態のReal-time evolution

一般状態では：

```text
|ψ(t)⟩ = e^{-iHt}|ψ(0)⟩
```

となり：

時間とともに

状態が混ざります。

これは：

干渉現象

の原因になります。

---

# Imaginary-time evolutionとは何か？

Imaginary-time evolutionは：

時間を虚数に置き換えた操作

です。

つまり：

genui{"math_block_widget_always_prefetch_v2": {"content": "U(\tau) = e^{-H\tau}"} }

ここで：

τ は imaginary time

です。

---

# なぜ虚時間を使うのか？

理由：

基底状態が強調される

からです。

重要な性質：

```text
e^{-Hτ}
```

を適用すると：

高エネルギー状態が消える

という特徴があります。

---

# 基底状態への収束

状態を：

固有状態の重ね合わせとして書くと：

```text
|ψ⟩ = c₀|E₀⟩ + c₁|E₁⟩ + ...
```

imaginary-time evolution後：

```text
|ψ(τ)⟩ ≈ c₀ e^{-E₀τ}|E₀⟩
```

になります。

つまり：

基底状態だけ残ります 🎯

---

# VQEとの関係

実は：

VQEは

imaginary-time evolutionの近似

として理解できます。

つまり：

```text
imaginary-time evolution
≈ variational optimization
```

です。

これは：

Variational Quantum Imaginary Time Evolution（VarQITE）

と呼ばれます。

---

# なぜ実機で直接実装できないのか？

問題：

```text
e^{-Hτ}
```

は：

ユニタリではない

からです。

量子回路は：

ユニタリ操作のみ

実装できます。

---

# 解決方法：Variational近似

そこで：

次の方法を使います：

```text
non-unitary evolution
↓
parameterized circuitで近似
```

これが：

VarQITE

です。

---

# VarQITEとは何か？

Variational Quantum Imaginary Time Evolution：

変分法を使って

imaginary-time evolution

を近似する手法です。

つまり：

```text
Ansatz更新
+
古典最適化
```

で実装します。

---

# Real-time evolutionのVariational版

同様に：

Real-time evolutionにも：

Variational版があります：

```text
VarQRTE
```

です。

正式名称：

Variational Quantum Real-Time Evolution

です。

---

# VarQRTEの目的

VarQRTEは：

時間発展を

浅い回路で近似

する方法です。

つまり：

```text
長い回路 ❌
浅い回路 ⭕
```

という

NISQ向け設計

になります 🚀

---

# Real-time と Imaginary-time の違い

比較：

| 種類             | 目的       |
| -------------- | -------- |
| Real-time      | ダイナミクス解析 |
| Imaginary-time | 基底状態探索   |

です。

つまり：

```text
Real-time → 物理シミュレーション
Imaginary-time → 最適化問題
```

になります。

---

# Hamiltonian Simulationとの関係

整理：

| 手法                     | 操作               |
| ---------------------- | ---------------- |
| Hamiltonian Simulation | e^{-iHt}         |
| QAOA                   | exp(-iγH)        |
| VQE                    | imaginary-time近似 |

つまり：

すべて：

時間発展アルゴリズム

として統一できます。

---

# Qiskitでの実装

Qiskitでは：

次のクラスが用意されています：

```python
from qiskit_algorithms.time_evolvers import (
    TrotterQRTE,
    VarQRTE,
    VarQITE
)
```

それぞれ：

| クラス         | 内容           |
| ----------- | ------------ |
| TrotterQRTE | Trotter実時間発展 |
| VarQRTE     | 変分実時間発展      |
| VarQITE     | 変分虚時間発展      |

です。

---

# VarQITEの直感

VarQITEは：

次の操作を繰り返します：

```text
パラメータ更新
↓
エネルギー減少
↓
基底状態へ近づく
```

つまり：

VQEの動的バージョン

です。

---

# VarQRTEの直感

VarQRTEは：

状態を追跡します：

```text
初期状態
↓
時間発展
↓
新しい状態
```

これを：

浅い回路で近似します。

---

# 応用分野

Real-time evolution：

```text
量子ダイナミクス
スピン輸送
量子情報伝播
```

Imaginary-time evolution：

```text
基底状態探索
量子化学
多体系問題
最適化問題
```

です。

---

# ここまでで何ができるようになったか？

できるようになったこと：

* Real-time evolution理解
* Imaginary-time evolution理解
* VarQRTE理解
* VarQITE理解
* VQEとの関係理解
* Hamiltonian Simulationとの統合理解

つまり：

時間発展アルゴリズムの全体像

が見えてきました ✨

---

# 次回への接続

次回は：

**ノイズとError Mitigation**

を扱います。

ここから：

実機実行に不可欠な知識

に入ります。

---

# 本章のまとめ

今回は：

* Real-time evolution
* Imaginary-time evolution
* VarQRTE
* VarQITE
* VQEとの関係
* Hamiltonian Simulationとの統一理解

を整理しました。

次回は：

NISQ時代の最大の課題：

ノイズ対策

を扱います。
