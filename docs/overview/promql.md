# promQL

探したいデータを取得できるように、どのようなデータがPrometheusで収集できているのかまず大づかみに把握しましょう。

## データ収集設定の確認

- 収集するデータの一覧は [Status] -> [Targets] で確認ができます。
- `prometheus.yml`で定義している、`scrape_configs`の設定によって決まります。

![image](https://user-images.githubusercontent.com/24913906/79410082-6dcd7180-7fda-11ea-9535-69f14fcd5ada.png)

`sd`とは`service discovery`の略語で、例では監視対象を別ファイルで定義しています。  
動的に検出したい場合は、以下公式より環境にあったものを選ぶ形となります。
[Configuration | Prometheus](https://prometheus.io/docs/prometheus/latest/configuration/configuration/)

## データの確認

- PrometheusのUIからクエリを実行できます
- メトリクス名に一部でもヒットするとサジェストで表示されます
- Executeボタンをクリックすると、[console],[graph]タブからデータを確認できます。
![image](https://user-images.githubusercontent.com/24913906/79416016-a9236c80-7fe9-11ea-84db-5712b583a29b.png)

## データ構造

メトリクス名とラベル（key/valueペア）で構成され、それぞれのラベルは異なる時系列を表します。

- メトリクス名
  - 名称は通常、メトリクスの機能を表す。
  - 例えば、 `node_filesystem_free_bytes`
    - ファイルシステム毎の空きディスク容量を表します。

- ラベル
  - 同じ時系列の異なる次元を識別するために使われます。
  - 例えば、`node_filesystem_free_bytes{device="/dev/sda2”}`
    - ファイルシステム「/dev/sda2」の空きディスク容量を表します。

メトリクスを決めてデータを取得するイメージする際には横軸・縦軸の理解が重要になります。

![image](https://user-images.githubusercontent.com/24913906/79417255-73cc4e00-7fec-11ea-9401-7f4e643a35ad.png)

## メトリクスのタイプ

PrometheusのClientライブラリは4つの主なタイプのメトリクスをサポートします。

| 種類        | 説明                                                                     | 例   |
| --------- | ---------------------------------------------------------------------- | --- |
| Counter   | 累積的なメトリクスでは、そのメトリクスが累積されるか、または0にリセットされます。 | - リクエスト数 </br> - リクエストのエラー数     |
| Gauge     | 時間に依存しない、任意に変更するメトリクスに使えます。                                | - メモリ使用量 </br> - CPU使用量    |
| Histogram | ある範囲内の時間におけるデータのサンプリングを行い、設定可能な単位毎にカウントする     |hoge|
| Summary   | Histogramと似ており、違いはカウント結果の合計をを同時に提供する  |huga|

<!-- Histogramは事前に統計値の集合が予期できる場合に各統計値の出現回数を数えるといった用途に向いている -->
<!-- Summaryは中央値や最大値/最小値といった分布に関するデータを扱いたい場合に向いている -->

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

チュートリアルで触ってもらうときに色々試してみてください！

---

<p style="text-align:center"> <a href="./prometheus"><- 前へ </a> | <a href="../"> Top </a> |<a href="../tutorial/iaas_building">次へ-> </a></p>
