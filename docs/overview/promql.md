# promQL

探したいデータを取得できるように、どのようなデータがPrometheusで収集できているのかまず大づかみに把握しましょう。

## データ収集設定の確認

- 収集する監視対象の一覧はPrometheusのUIから確認することができます
![image](https://user-images.githubusercontent.com/24913906/79410082-6dcd7180-7fda-11ea-9535-69f14fcd5ada.png)

## データの確認

- PrometheusのUIからクエリを実行することができます。
- メトリクス名に一部でもヒットするとサジェストで表示されます。
- Executeボタンをクリックすると、[console],[graph]タブからデータを確認できます。
![image](https://user-images.githubusercontent.com/24913906/79416016-a9236c80-7fe9-11ea-84db-5712b583a29b.png)

## データの構造

メトリクス名とラベル（key/valueペア）で構成され、それぞれのラベルは異なる時系列を表します。

- メトリクス名
  - 名称は通常、メトリクスの機能を表します。
  - 例えば、 `node_filesystem_free_bytes`
    - ファイルシステム毎の空きディスク容量を表します。

- ラベル
  - 同じ時系列の異なる次元を識別するために使われます。
  - 例えば、`node_filesystem_free_bytes{device="/dev/sda2”}`
    - ファイルシステム「/dev/sda2」の空きディスク容量を表します。

メトリクスを決めてデータを取得するイメージする際には横軸・縦軸の理解が重要になります。

![image](https://user-images.githubusercontent.com/24913906/79417255-73cc4e00-7fec-11ea-9401-7f4e643a35ad.png)


## promQLでできること

監視対象のメモリ関連のメトリクスを例に確認していきます。

- データを参照する
  - ラベル指定
    - `node_filesystem_free_bytes{device="/dev/sda2"}`
  - 曖昧検索
    - `node_filesystem_free_bytes{device=~"^/.*/sda[0-9]"}`
- データを計算する
  - 四則演算
    - `node_filesystem_avail_bytes{device="/dev/sda2"} + node_filesystem_free_bytes{device="/dev/sda2"}`
    - `ceil(node_filesystem_free_bytes{device="/dev/sda2"} / 1024 /1024)`
- データを集計する
  - 最大/最小値
    - `max(node_filesystem_free_bytes)`
    - `min(node_filesystem_free_bytes)`
  - 合計/平均
    - `sum(node_filesystem_free_bytes)`
    - `avg(node_filesystem_free_bytes)`
  - グルーピング
    - `max(node_filesystem_free_bytes) by ( mountpoint, fstype)`

## まとめ

独自クエリというところで身構えてしまうところもありますが、
実際触ってみると直感的にデータを取り扱うことができましたね。

ここまでで紹介しきれていない機能もたくさんありますが、  
まずはPrometheusの世界感を実際に触れながら学んでください！

---

<p style="text-align:center"> <a href="./prometheus"> &lt; 前へ </a> | <a href="../"> Top </a> | <a href="../tutorial/iaas_building"> 次へ -&gt; </a> </p>
