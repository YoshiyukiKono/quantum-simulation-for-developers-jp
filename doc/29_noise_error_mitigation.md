以下は **フラットな Markdown** です。そのまま
`doc/29_noise_error_mitigation.md` として保存できます。

---

# doc/29_noise_error_mitigation.md

# 第29回 ノイズとError Mitigation ― NISQ実機で計算するための必須技術

## はじめに

前回は：

* Real-time evolution
* Imaginary-time evolution
* VarQRTE
* VarQITE
* 時間発展アルゴリズムの統一理解
* VQEとの関係

を整理しました。

ここまでで：

NISQアルゴリズムの理論構造

はほぼ完成しました。

しかし：

実機で動かすと

理論どおりには動きません。

理由は：

**ノイズ（noise）**

です。

今回は：

NISQ時代の最大の課題：

Error Mitigation（誤差緩和）

を扱います。

---

# なぜノイズが問題になるのか？

量子コンピュータは：

非常に繊細な装置です。

つまり：

```text
状態がすぐ壊れる
```

という特徴があります。

例：

* ゲート誤差
* 測定誤差
* デコヒーレンス
* クロストーク

などがあります。

---

# ノイズとは何か？

ノイズとは：

理想的な量子操作からのズレ

です。

つまり：

```text
理想回路
↓
実機回路
↓
誤差発生
```

という構造になります。

---

# なぜNISQでは特に重要なのか？

NISQとは：

Noisy Intermediate-Scale Quantum

の略でした。

つまり：

```text
ノイズがある前提の量子計算
```

です。

したがって：

ノイズ対策なしでは

意味のある計算になりません。

---

# 主なノイズの種類

代表例：

| ノイズ           | 内容        |
| ------------- | --------- |
| Gate error    | ゲートが正確でない |
| Readout error | 測定が間違う    |
| Decoherence   | 状態が壊れる    |
| Crosstalk     | 他量子ビットの影響 |

です。

---

# Decoherenceとは何か？

量子状態は：

時間とともに壊れます。

これは：

環境との相互作用

によって発生します。

代表例：

```text
T1 緩和
T2 位相緩和
```

です。

---

# T1とは何か？

T1：

エネルギー緩和時間

です。

つまり：

```text
|1⟩ → |0⟩
```

へ自然に戻ります。

---

# T2とは何か？

T2：

位相緩和時間

です。

つまり：

```text
重ね合わせが壊れる
```

現象です。

これは：

干渉を破壊します。

---

# Readout errorとは何か？

測定時：

```text
|0⟩ → 1 と誤判定
|1⟩ → 0 と誤判定
```

が発生します。

これは：

最も典型的なノイズです。

---

# Gate errorとは何か？

理想操作：

```text
RX(θ)
```

実機操作：

```text
RX(θ + ε)
```

となります。

つまり：

回転角がズレます。

---

# なぜError CorrectionではなくMitigationなのか？

理想：

Error Correction（誤り訂正）

現実：

Error Mitigation（誤差緩和）

です。

理由：

誤り訂正には

大量の量子ビットが必要

だからです。

---

# Error Mitigationとは何か？

Error Mitigationとは：

誤差を補正するのではなく

影響を減らす

技術です。

つまり：

```text
完全修正 ❌
統計補正 ⭕
```

です。

---

# 主なError Mitigation技術

代表例：

```text
Readout error mitigation
Zero-noise extrapolation
Probabilistic error cancellation
Measurement error mitigation
```

です。

---

# Readout error mitigationとは？

測定誤差を補正します。

方法：

```text
誤差行列を推定
↓
逆行列で補正
```

です。

---

# 誤差行列とは何か？

例：

```text
P(測定結果 | 真の状態)
```

を表す行列です。

つまり：

```text
観測値
↓
補正
↓
真の値推定
```

が可能になります。

---

# QiskitでのReadout error mitigation

Qiskitでは：

測定誤差補正が可能です：

```python
from qiskit_ibm_runtime import Sampler
```

と組み合わせて：

補正を実行できます。

---

# Zero-noise extrapolationとは？

ノイズ量を増やして：

外挿する方法です。

処理：

```text
低ノイズ計算
中ノイズ計算
高ノイズ計算
↓
ノイズゼロへ外挿
```

です。

---

# なぜ外挿できるのか？

理由：

ノイズは：

滑らかに増加する

と仮定するからです。

つまり：

```text
noise → 0
```

へ近づけます。

---

# Probabilistic error cancellationとは？

理想操作を：

確率的に再構成する方法

です。

構造：

```text
理想ゲート
≈ ノイズ付きゲートの線形結合
```

になります。

---

# ただし計算コストが高い

問題：

サンプリング回数が増える

ことです。

つまり：

```text
精度 ⬆
計算コスト ⬆
```

になります。

---

# Error mitigationの直感

Error mitigationは：

次の操作です：

```text
ノイズを消す ❌
ノイズを推定 ⭕
ノイズを補正 ⭕
```

です。

---

# VQEとError Mitigation

VQEは：

Error mitigationと相性が良いです。

理由：

```text
期待値測定
+
統計補正
```

が可能だからです。

---

# QAOAとError Mitigation

QAOAでも：

重要です。

特に：

```text
浅い回路
+
多数ショット
```

という特徴があるため：

補正しやすいです。

---

# 実機実行の基本戦略

実機では：

次の順序で設計します：

```text
回路を浅くする
↓
測定数を増やす
↓
Error mitigation適用
```

です。

---

# NISQ設計の基本原則

重要：

```text
深い回路 ❌
短い回路 ⭕
```

です。

これは：

Error mitigationと組み合わせることで

最大効果を発揮します。

---

# 実機向けAnsatz設計

推奨：

```text
Hardware-efficient Ansatz
QAOA Ansatz
Problem-informed Ansatz
```

です。

理由：

回路が浅いからです。

---

# ノイズと量子優位性の関係

量子優位性とは：

ノイズの影響を超える計算

です。

つまり：

```text
signal > noise
```

が必要です。

---

# ここまでで何ができるようになったか？

できるようになったこと：

* ノイズの種類理解
* T1 / T2理解
* Readout error理解
* Zero-noise extrapolation理解
* Probabilistic error cancellation理解
* Error mitigation戦略理解

つまり：

実機で量子アルゴリズムを動かす準備

が整いました 🚀

---

# 次回への接続

次回は：

**測定戦略とショット設計**

を扱います。

ここで：

実機計算の精度を決める要素

を理解します。

---

# 本章のまとめ

今回は：

* ノイズとは何か
* Decoherence
* T1 / T2
* Readout error mitigation
* Zero-noise extrapolation
* Probabilistic error cancellation
* 実機向け設計戦略

を整理しました。

次回は：

測定回数（shot数）とPauli grouping

を含む

測定最適化技術

を扱います。
