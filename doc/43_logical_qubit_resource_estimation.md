以下は **フラットな Markdown** です。そのまま
`doc/43_logical_qubit_resource_estimation.md` として保存できます。

---

# doc/43_logical_qubit_resource_estimation.md

# 第43回 論理量子ビットの資源見積もり ― なぜ数百万qubitが必要なのか？

## はじめに

前回は：

* Surface codeの構造
* stabilizer測定
* syndrome
* code distance
* logical error rate
* lattice surgery
* braiding

を整理しました。

今回は：

実用量子コンピュータに必要な

**論理量子ビット数と物理量子ビット数の関係**

を扱います。

テーマ：

```text
logical qubitとは何か？
physical qubitは何個必要か？
distanceはどう決まるのか？
resource estimationとは何か？
```

を理解します。

---

# なぜ資源見積もりが重要なのか？

量子計算では：

アルゴリズムの理論性能だけでは足りません。

重要なのは：

```text
実機で実行できるか？
```

です。

その判断に必要なのが：

resource estimation

です。

---

# resource estimationとは何か？

定義：

量子アルゴリズム実行に必要な

```text
logical qubit数
physical qubit数
gate数
runtime
```

を推定することです。

---

# physical qubitとlogical qubitの違い

復習：

| 種類             | 意味           |
| -------------- | ------------ |
| physical qubit | 実機の量子ビット     |
| logical qubit  | 誤り訂正された量子ビット |

重要：

```text
logical qubit ≠ physical qubit
```

です。

---

# logical qubitはどう作られるのか？

Surface codeでは：

1 logical qubit は

```text
distance × distance
```

サイズの格子で構成されます。

つまり：

distanceが増えるほど

必要qubit数が増えます。

---

# distanceとは何を意味するのか？

distanceとは：

論理エラーが発生するまでに必要な

最小誤り数

です。

直感：

```text
distance ↑
→ 安定性 ↑
→ qubit数 ↑
```

です。

---

# logical error rateとの関係

Surface codeでは：

logical error rate は：

distanceに対して

指数関数的に減少します。

つまり：

```text
distanceを少し増やすだけで
安全性が大きく向上
```

します。

これは非常に重要です 📉

---

# 物理誤り率とdistanceの関係

必要distanceは：

次の条件で決まります：

```text
physical error rate
+
必要計算時間
+
必要精度
```

です。

---

# 典型的な例

仮に：

```text
physical error rate ≈ 10⁻³
```

とすると：

安全な計算には：

```text
distance ≈ 25〜35
```

程度が必要になります。

---

# logical qubitあたりのphysical qubit数

Surface codeでは：

おおよそ：

```text
physical qubits ≈ 2 × distance²
```

になります。

例：

distance = 25

なら：

```text
≈ 1250 physical qubits
```

必要になります。

---

# なぜ2倍なのか？

理由：

Surface codeでは：

```text
data qubit
ancilla qubit
```

の両方が必要だからです。

---

# logical qubit数はどう決まるのか？

これは：

アルゴリズムによって決まります。

例：

| アルゴリズム         | 必要logical qubit |
| -------------- | --------------- |
| 小規模VQE         | 10〜100          |
| 化学計算           | 100〜1000        |
| Shor (RSA2048) | 数千              |

です。

---

# Shorアルゴリズムの資源見積もり

RSA2048を解くには：

概算：

```text
logical qubit ≈ 4000
```

必要です。

---

# physical qubit換算

もし：

```text
distance ≈ 30
```

なら：

1 logical qubit ≈ 1800 physical qubits

となります。

つまり：

```text
4000 × 1800 ≈ 約720万 physical qubits
```

必要になります。

---

# なぜそんなに多いのか？

理由：

誤り訂正のオーバーヘッドです。

つまり：

```text
量子計算は
誤り訂正が支配する
```

と言えます。

---

# Tゲートの影響

FTQCでは：

計算コストの大部分は：

```text
T gate
```

によって決まります。

理由：

magic state distillation

が必要だからです。

---

# Tゲート数の重要性

resource estimationでは：

次が重要になります：

```text
T-count
```

つまり：

必要Tゲート数です。

---

# runtime estimationとは何か？

もう1つ重要なのが：

実行時間です。

これは：

```text
T-depth
```

で評価されます。

意味：

```text
Tゲートの直列長
```

です。

---

# なぜT-depthが重要なのか？

理由：

magic state生成には

時間がかかるからです。

つまり：

```text
T-depth ↑
→ runtime ↑
```

になります。

---

# magic state factoryとは何か？

FTQCでは：

専用領域：

```text
magic state factory
```

を構築します。

役割：

```text
Tゲート用状態を生成する
```

です。

---

# factoryが必要な理由

理由：

Tゲートは：

直接実装できない

からです。

代わりに：

```text
magic state注入
```

を使います。

---

# logical qubitの構成要素

FTQCでは：

logical qubitは：

次の領域で構成されます：

```text
data block
ancilla block
magic state factory
routing領域
```

です。

---

# routingとは何か？

意味：

logical qubit同士の通信

です。

Surface codeでは：

```text
lattice surgery
```

を使います。

---

# resource estimationの基本手順

流れ：

```text
アルゴリズム選択
↓
logical qubit数推定
↓
T-count推定
↓
distance決定
↓
physical qubit換算
↓
runtime推定
```

です。

---

# なぜdistanceを固定できないのか？

理由：

必要精度が変わるからです。

つまり：

```text
短い計算 → 小distance
長い計算 → 大distance
```

になります。

---

# 実用量子計算に必要な規模

現在の予測：

| 用途      | 必要physical qubit |
| ------- | ---------------- |
| 小規模FTQC | 数十万              |
| 化学計算    | 数百万              |
| RSA2048 | 数百万〜千万           |

です。

---

# 現在の量子コンピュータとの比較

現在：

```text
≈ 100〜1000 physical qubits
```

です。

つまり：

まだ

3〜4桁不足

しています。

---

# では悲観すべきなのか？

答え：

いいえ。

理由：

量子ハードウェアは：

指数的に改善しています。

つまり：

```text
スケールは時間の問題
```

です。

---

# 開発者視点での意味

重要：

resource estimationを理解すると：

```text
どのアルゴリズムが現実的か？
```

が判断できます。

これは：

研究でも産業でも重要です。

---

# NISQとの違い

NISQでは：

```text
qubit数 = 数十〜数百
```

でした。

FTQCでは：

```text
qubit数 = 数百万
```

になります。

ここが最大の違いです。

---

# 将来の量子ソフトウェア設計

FTQC時代には：

次が重要になります：

```text
T-count削減
circuit compression
logical routing最適化
magic state共有
```

です。

---

# resource estimationツール

代表例：

```text
Qiskit resource estimator
Azure Quantum estimator
OpenFermion
```

などがあります。

これらは：

将来の量子設計の基盤になります。

---

# 次回への接続

次回は：

**量子コンピュータはどの問題で本当に優位になるのか？**

を扱います。

テーマ：

```text
Shorの現実的影響
量子化学の可能性
最適化問題の未来
量子機械学習の評価
```

シリーズはいよいよ：

量子優位性の最終整理

に入ります 🚀

---

# 本章のまとめ

今回は：

* logical qubitとphysical qubit
* code distance
* T-count
* T-depth
* magic state factory
* routing
* runtime estimation
* resource estimation

を整理しました。

次回は：

**量子コンピュータが本当に強い問題領域**

を体系的に整理します。
