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

<!-- テーブルにする -->

| 種類        | 説明                                                                     | 例   |
| --------- | ---------------------------------------------------------------------- | --- |
| Counter   | 累積的なメトリクスでは、そのメトリクスが累積されるか、または0にリセットされます。例えば、 |一つのHTTPにエラーが出た回数をカウントします。     |
| Gauge     | メモリ使用率など、時間に依存しない、任意に変更するメトリクスに使えます。                                |     |
| Histogram | 主にはある範囲内の時間におけるデータ・サンプルの表示に使い、ヒストグラム(柱状グラフ)の形が想像できます。                  |     |
| Summary   | Histogramに似て、ある範囲内の時間におけるデータ・サンプルのサマリーを表示します。                          |     |

## promQLでできること

- データを参照する
  - TBD
- データを計算する
  - TBD
- データを集計する
  - TBD

チュートリアルで触ってもらうときに色々試してみてくださいね！

---

<p style="text-align:center"> <a href="http://hoge"><- 前へ </a> | <a href="http://hoge"> Top </a> |<a href="http://hoge">次へ-> </a></p>
