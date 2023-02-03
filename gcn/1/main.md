# [グラフニューラルネットワーク<!-- .element: class="r-fit-text" -->](https://www.amazon.co.jp/dp/4274228878/)
## [PyTorchによる実装](https://www.amazon.co.jp/dp/4274228878/)
### 村田剛志

<!-- <img src="https://github.com/atarum/GraphNeuralNetworks/raw/main/GNN.jpg" height=300 style="text-align: left"> -->

M2 伊藤 <!-- .element: style="text-align: right" -->

---
## 今日扱うこと

- 準備運動
- 1章 グラフニューラルネットワークとは
- 2章 グラフエンべディング

※ 実装については扱いません

---

# 準備運動

>>>

## グラフとは？

![](https://i.imgur.com/4t80uAw.png)

[グラフ理論入門](https://qiita.com/maskot1977/items/e1819b7a1053eb9f7d61)より引用

>>>
## グラフの汎用性

<small>
<div id="left">

この世のものはたいていグラフ

- SNSのフォロー関係
- 地図
- 分子構造
- ゲーム理論
- ディレクトリ構造
- 構文
- ニューラルネットワーク

→様々な分野に応用可能

</div>

<div id="right">

様々なアルゴリズムが利用可能

- 最短距離
    - ダイクストラ
    - ワーシャル=フロイド法など
- Graph Neural Network
    - 埋め込みさえ得られれば色々できる
- スペクトラルグラフ理論
- グラフ分解
- マッチング
</div>
</small>

---

## 第1章 <!-- .element: style="text-align: left" -->
# グラフニューラルネットワークとは<!-- .element: class="r-fit-text" -->

>>>

## CNN VS GNN

<div id="right">

CNN

<small>

- 対象: 画像
- 複雑さ: 最近傍のみ接続
- 隣接頂点数: 固定

</small>
<img src="https://user-images.githubusercontent.com/31335755/214237535-7fc4759e-d349-47e3-b7c9-dffcc8dcf92d.png" width=300>
</div>
<div id="left">

GNN
<small>

- 対象: グラフ
- 複雑さ: 離れた頂点が接続
- 隣接頂点数: 可変

</small>
<img src="https://user-images.githubusercontent.com/31335755/214237648-a9500a2b-5dcf-43e3-bfd1-60ec308f1505.png" width=300>
</div>

図は [A Comprehensive Survey on Graph Neural Networks](https://arxiv.org/pdf/1901.00596.pdf)より
<!-- .element: class="r-fit-text" -->

>>>

## GNNの難しいところ

- 隣接頂点数が可変
- 局所性が保たれない
- ノードが順序付けられない

>>>

## グラフに対する機械学習
<small>

- ノード分類
  - 人を頂点，友人関係をエッジ
  - 人の所属コミュニティの予測
- グラフ分類
  - 原子を頂点，結合をエッジ
  - 水溶性の有無，毒性の有無を予測
- リンク予測
  - 商品とユーザーをノード，購買をエッジ
  - 将来の購買を予測
- グラフ生成
  - 原子を頂点，結合をエッジ
  - 同様の特徴を持つ化合物を生成

</small>

>>>

## GNNの応用
- 画像認識
- 推薦システム
- 交通量予測
- 化合物分類
- 組み合わせ最適化
- COVID-19
  - 薬剤設計，薬剤転用，疫学的予測，需要予測，感染追跡

---

# 第2章 <!-- .element: style="text-align: left" -->
# グラフエンべディング <!-- .element: class="r-fit-text" -->

>>>

## Embedding(埋め込み)とは

- `$G = (V, E)$`
  - `$n = |V|$`
  - `$m = |E|$`
- `${\bf A} \in \mathbb{R}^{n\times n}$`: 隣接行列
  - `$n \times n$`行列
  - `${\bf A}_{ij}$`: 頂点$i$と$j$が接続されてれば1，他0の行列

各頂点$v$を，情報を保持して`$\mathbb{R}^k(k \ll n)$`に圧縮

>>>

## Embeddingのいいところ

- アルゴリズムの高速化
  - 最短距離 <!-- .element: class="fragment highlight-red" -->など
- 深層学習含む，既存の機械学習手法が使える
- 2，3次元に埋め込んで可視化
- 構造情報だけでなく，属性情報も埋め込める
- 並列，分散化が容易

>>>

## 未知の課題も多い

- どんな埋め込みが良い？
- どんなタスクができる？

研究のネタになるかも？

>>>

## 次元縮約に基づく手法
高次元データを低次元に埋め込む手法

グラフ以外にも適用可能

- 主成分分析 (Principal Component Analysis, PCA)
- 多次元尺度法 (Multi Dimensional Scaling, MDS)
- 特異値分解 (Singular Value Decomposition, SVD)
- Isomap

構造情報や属性情報を考慮しにくい

>>>

## グラフ構造に基づく手法
- DeepWalk
- LINE
- node2vec <!-- .element: class="fragment highlight-red" -->
- GraRep

>>>

## word2vec
Word2Vecは単語をベクトルに変換する手法

- I / like / banana.
- You / are / gorilla.

単語のリストである文，のリストから学習

**↓** <!-- .element: class="fragment" data-fragment-index="1" -->

**頂点のリスト(文)のリストがあれば学習できそう** <!-- .element: class="fragment" data-fragment-index="1" -->

>>>

## Random Walk
<!-- using d3js -->
<!-- <div class="fig-container" data-file="fig.html"></div> -->

- 確率的に隣接頂点に移動
  - 確率は一様だったり，何らかの関数で決定されたり
- ある頂点からランダムウォークして適当な長さの頂点リストをサンプリング
- 様々な頂点から行い，頂点リストのリストを獲得


>>>

## どういうランダムウォークにする？ <!-- .element: class="r-fit-text" -->

- 幅優先探索(BFS)でミクロっぽく
  - 構造同値(structural equivalence)を反映
    - ブリッジやハブなど
  - グラフの一部しか見れない
- 深さ優先探索(DFS)でマクロっぽく
  - 同類性(homophily)を反映
    - コミュニティ構造など
  - 出発ノードとの関係は見れない

これがnode2vecの，一番大きいアピールポイント <!-- .element: class="r-fit-text" -->

>>>

## Random Walk

<img src="https://makitat.com/wp-1/wp-content/uploads/2020/05/python_plotly-1024x995.png" height=600, width=700>

>>>

## Random Walkの確率
頂点$t$から隣接頂点$v$に移動したとき，$v$の隣接頂点$x$に移動する確率
<!-- .element: class="r-fit-text" -->

`$$
\pi_{vx} = \alpha_{pq}(t, x)\cdot w_{vx}
$$`

- `$\alpha_{pq}(t, x)$`
  - パラメータ`$p, q$`から決定される値
  - $t$は1ステップ前，$x$は1ステップ後の頂点
  - DFS，BFSの気持ちが込められている
- `$w_{vx}$`
  - 頂点`$v$`と`$x$`を接続するエッジの重み

>>>

## 関数`$\alpha_{pq}$`の定義

`$$
\alpha_{pq}(t, x)=
\left\{
\begin{array}{ll}
\frac{1}{p} & \text{if } d_{tx}=0，同じ頂点tに戻る \\
1 & \text{if } d_{tx}=1，t, v両方と接続される頂点 \\
\frac{1}{q} & \text{if } d_{tx}=2，tとは接続しない頂点
\end{array}
\right.
$$`
<!-- .element: class="r-fit-text" -->

- `$p$`が大きければ同じ頂点に戻る確率が低い
- `$q$`が大きければ`$t$`から離れた頂点に行く確率が低い

>>>

## このサンプリングの嬉しいところ<!-- .element: class="r-fit-text" -->
- $p, q$次第でDFSとBFSの中間のような探索が可能
- それぞれのrandom walkは独立なので並列化可能
- グラフの一部が変化しても，その部分のランダムウォークのみ更新すれば良い

得られた頂点リストを用いて埋め込みを学習


>>>

## 目的関数
**$Pr$に注目**
<!-- .element: style="text-align: left" -->

`$$
\min_{\phi}-\log Pr(\{v_{i-w}, \cdots, v_{i+w}\}\setminus v_i|\phi(v_i))
$$`

- $\phi:V \to \mathbb{R}^d$
  - $v\in V$の埋め込みを得る写像
- $Pr$
  - 頂点$v_i$と`$\{v_{i-w}, \cdots, v_{i+w}\}\setminus v_i$`が同じ頂点列(集合)に属する確率

このようなタスクを**skip-gram**と呼ぶ

>>>


`$$
Pr(\{v_{i-w}, \cdots, v_{i+w}\}\setminus v_i|\phi(v_i))
$$`

$\phi$がどんな関数になるかわからない

# ↓
<!-- .element: class="fragment" data-fragment-index="1"-->

# ニューラルネットワークで表現しよう
<!-- .element: class="r-fit-text fragment" data-fragment-index="1"-->

>>>

## 注意
- 頂点→単語，頂点列→文章としたのがword2vec
- 適当にサンプリングしてSGDで最適化したのがDeepWalk
- もともとの目的関数は異なる
  - 計算量の観点などから，解くことが難しい
  - いくつかの問題緩和から目的関数を変形
    - 頂点列の順番を無視など


詳しくは本を読んでみてください

---

## まとめ

- グラフには様々なタスクが存在する
- 埋め込みで機械学習的アプローチが可能になる
- node2vecは，頂点列のサンプリングを工夫
  - DFS/BFSを使い分ける
- node2vec, DeepWalkはskip-gramで学習
