# kaggle
## 機械学習で役立つTips
### LightGBMと他のブースティングのまとめ（PyDataでのスライド）  
https://alphaimpact.jp/downloads/pydata20190927.pdf
XGBoostと比べ、並列化による高速化、大規模データセットへの対応が可能となった。  
ヒストグラムによる学習→連続値をbinにまとめることで計算量を減らしている。  
深さベースではなく、葉ベースでの学習  
短所：カテゴリカル特徴量が多すぎると役に立たない。→CatBoostの利用を考える。  
重要なパラメータたち:  
num_leaves:ツリーの葉の数。2^(max_depth)より小さくする必要あり。  
max_depth：ツリーの深さの最大値7がちょうどいいことが多いらしい。  
min_child_samples (min_data_in_leaf):葉を作るのに必要な最低サンプル数。データが少なく厳しいときは、これを小さくする。  
min_child_weight (min_sum_hessian_in_leaf):葉を分割するのに必要な（ロスの）hessianの合計値。小さければ小さいほどロスを小さくしようと葉を分割するが、それはオーバーフィッティングを引き起こす。  
subsample (bagging_fraction):バギングの割合（訓練データの何パーセントを利用するか）  
subsample_freq:バギングを行う間隔  
colsample_bytree (feature_fraction):特徴量サンプリングの割合（何パーセントの特徴量を利用するか）  
min_split_gain:葉を分割する条件として設定するロス改善度の最小値。この値以上の改善が無ければ葉を分割しない。  
reg_alpha:L1正則化。過学習していそうなら調整する。  
reg_lambda:L2正則化。過学習していそうなら調整する。。

learning_rateは0,01の固定でよい。チューニングする必要はない

## 不均衡データの処理
・アンダーサンプリング：多いデータを減らす。データの選択は重複選択なしがよい。事前にデータをクラスタリングし、クラスごとにサンプリングを行う方法がある。アンダーサンプリングは情報量を減らしてしまうため、なるべくオーバーサンプリングが好まれる。
・オーバーサンプリング：少ないデータを増やす。代表的な手法はSMOTE。データ間の直線間からサンプリングする手法ため次元数が大きくなる場合は偏りが大きくなる。その場合はバギング（多数のモデルをアンサンブルすること）することで解消できる。

## 欠損値補完
・定数による補完：欠損が多いとデータの分散が本来のものと大きくかけ離れやすい  
・集計値による補完：平均、中央、最大、最小値をいれる  
・他のデータに基づく予測値：すでにあるデータから機械学習モデルを作成し、欠損部分の予測を行う  
・時系列の関係から補完：時間に対して連続している値はMCAR、MARが有効  
・多重代入法：補完したデータを複数作成し、結果を統合する。PMM.fancyimputeライブラリで実装可能。
・最尤法：潜在変数を導入し、EMアルゴリズムを用いて尤度を最大化することで補完する。

## 文字列処理
文章を扱うとき、語順を考慮に入れると複雑になるため、語順を考慮に入れないBag of Words を試すのが早い。  
MeCabを利用すると、日本語文章を形態素解析してくれるので便利。  
TF-IDF：TF(Term Frequency)文章内の出現割合（[対象の単語の数]/[文章に含まれる単語の合計数]）とIDF(Inverse Document Frequency)単語の出現割合
のスコア(log[全文書数]/[対象の単語が出現している文書数]＋1)を用いる。