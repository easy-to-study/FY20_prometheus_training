# 監視システムの種類

大きく「Push型アプローチ」と「Pull型アプローチ」と分けられます。

## Push型アプローチ

Push 型のアプローチでは、監視対象となるホストにエージェントをインストールし、各ホストのエージェントが監視サーバに対してデータを送信する形で実現がなされています。

管理サーバ側で設定変更を行わずとも、新しい監視対象が増えた場合には、対象のサーバにエージェントを追加するだけで監視対象を増やすことができます。Push 型の監視システムとして以下のようなものがあります。

- [Sensu](https://sensu.io/)
- [Datadog](https://www.datadoghq.com/ja/?utm_source=Advertisement&utm_medium=GoogleAdsNon1stTierBrand&utm_campaign=GoogleAdsNon1stTierBrand-JPNLangEN&utm_content=Brand&utm_keyword=%2Bdatadog&utm_matchtype=b&gclid=CjwKCAjwvtX0BRAFEiwAGWJyZOwjPjcc4rAofuU8c6ND9RFInP8XGbcaADPhdfuSkeGSv68Do0BrZhoCEU8QAvD_BwE)

## Pull型アプローチ

Pull 型アプローチでは、監視サーバ上に監視対象についての設定を行い、監視対象からデータを集めてくる形のアプローチがとられています。

古くからある監視システムはこちらのアプローチをとっていることが多いように思います。Pull 型の監視システムとしては以下のようなものがあります。

- [Zabbix](https://www.zabbix.com/jp)
- [Prometheus](https://prometheus.io/)

## アプローチのまとめ

![image](https://user-images.githubusercontent.com/24913906/79302727-1ff83100-7f28-11ea-8571-06493ad4b7da.png)

|       | Pull型アプローチ                    | Pushアプローチ型                        |
| ----- | ----------------------------- | --------------------------------- |
| メリット  | 関係のないサーバからのメトリクスを受け付けない       | 監視を行うサーバが増えても監視サーバ本体の設定を変更しなくても良い |
| デメリット | 監視を行うサーバが増えた際に監視サーバ本体の設定変更が必要 | 監視対象ではないサーバがメトリクスを投げても受けてしまう      |

監視ツールによって、特色（メリット・デメリット）や費用の関係もあるので使用目的にあったものをチョイスして利用することが重要だと思います。

Prometheusでは、pull型のデメリットの部分（監視を行うサーバが増えた際に監視サーバの設定変更が必要）をService Discoveryという、サーバを自動で検知してくれる仕組みが補ってくれているためPull型のデメリットである
部分を払拭しているといえます。

## Prometheusとは

![image](https://user-images.githubusercontent.com/24913906/79299452-0b636b00-7f1f-11ea-8c31-0fe2e93da8f0.png)

Prometheus は、サーバのリソース状況やソフトウェアの統計情報といった各種メトリクスを収集して監視を行うモニタリングシステムです。ロギングやトレーシングを行うといった多用途のものではなくメトリクス収集に特化しているモニタリングシステムで、人気が高まりつつあるモニタリングシステムです。

## 専門用語

- 時系列データ
  - 時間的に変化した情報を持つデータのことを言います。
  - 気温の遷移や降水状況などの気象観測など、ある一時期の状態を表すようなデータ
- メトリクス
  - 基準とすべき尺度や指標のことを言います。
- スクレイピング
  - メトリクスの収集することを言います。
- インスタンス
  - スクレイピングができるエンドポイントのことを指します
- ジョブ
  - 同じ目的を持つインスタンスの集まりのことを指します

```plain
例：4つのインスタンスを持つAPIサーバージョブ
ジョブ： api-server
    インスタンス1： 1.2.3.4:5670
    インスタンス2： 1.2.3.4:5671
    インスタンス3： 5.6.7.8:5670
    インスタンス4： 5.6.7.8:5671
```

## Prometheusの特徴

- バイナリを動かすだけで動作する
  - Golang製(クロスコンパイルによりさまさまなOS上で実行が可能となっている)
  - 言語環境の整備・プラグインが不要
  - 依存システムがない
    - 別でDBの用意をせずとも動作する
- ホストが増減する前提で設計されている
  - Auto Scalingなどによるホスト名・IPアドレスの変更に対応できる
- 柔軟な可視化
  - 様々なグラフ化とダッシュボードをサポート
- アラートを減らす思想
  - アラートのグルーピング・ミュート機能をサポート

## アーキテクチャ

Prometheus は複数のコンポーネントで構成されています。

![image](https://user-images.githubusercontent.com/24913906/79299342-c0495800-7f1e-11ea-89cd-a7c222d36b50.png)

> 引用: [Overview | Prometheus](https://prometheus.io/docs/introduction/overview/)

## コンポーネント

### Prometheus server

- 監視サーバーのプログラム
- 定期的に全ての exporter をポーリングしてリソース情報を収集する
- 監視したデータは prometheus 内の DB に保持される
- アラートの計算と発火を行う

### Exporter

- 監視対象サーバー上で動かすプログラム
- テキスト形式でリソース情報を公開するWeb API のようなもの
- 監視対象のリソース毎に exporter が用意されている
  - ない場合、Clientライブラリが提供されている言語であれば比較的簡単に自作することができる
    - [Exporters and integrations | Prometheus](https://prometheus.io/docs/instrumenting/exporters/)
    - 提供されている言語：go, python,java,ruby

### Pushgateway

- 主には一時的なジョブのプッシュに使用されます。
- このタイプのジョブは存在する期間が短く、PrometheusがPullを行えば消えるかもしれないため、ゲートウェイを使ってプッシュします。

### grafana

- ログ・データ可視化のためのツールで、Prometheus で取得したデータをカッコよく可視化するために利用されます。

### Alertmanager

- Prometheus Server からのアラート・イベントを受け取り、定義された通知設定 (例えばE-mail、slack 等) に基づいてアラートを送信します。

### Service discovery

- 監視対象を自動的に見つけることができる
- サポートしている主なプラットフォームは以下が挙げられる
  - Azure
    - Azure上のVMのディスカバリ
  - ec2
    - Amaozn EC2インスタンス向けのディスカバリ
  - gce
    - Google Cloud Platform向けのディスカバリ
  - kubernetes
    - kubernetes向けのディスカバリ

### コンポーネントのまとめ

![image](https://user-images.githubusercontent.com/24913906/79300874-f688d680-7f22-11ea-951e-d35b0ba58784.png)

## Prometheusのデータモデル

メトリクス名とラベル（key/valueペア）で構成され、それぞれのラベルは異なる時系列を表します。
モデルに関する情報は以下の通りです

### Metrics Name

名称は通常、メトリクスの機能を表す。
例えば、 `http_requests_total` はHTTPリクエストの総数を表します。

### ラベル

同じ時系列の異なる次元を識別するために使われます。
例えば、`http_request_total{method=”Get”}` はHTTPがGetしたリクエストの総数を表します。
よって、method=”Post”の時には、また別の新しいメトリクスになります。

## メトリクスのタイプ

PrometheusのClientライブラリは4つの主なタイプのメトリクスをサポートします

### Counter

累積的なメトリクスでは、そのメトリクスが累積されるか、または0にリセットされます。例えば、一つのHTTPにエラーが出た回数をカウントします。

### Gauge

メモリ使用率など、瞬時で時間に依存しない、任意に変更するメトリクスに使えます。

### Histogram

主にはある範囲内の時間におけるデータ・サンプルの表示に使い、ヒストグラム(柱状グラフ)の形が想像できます。

### Summary

Histogramに似て、ある範囲内の時間におけるデータ・サンプルのサマリーを表示します。

## Prometheusの運用プロセス

1. 監視対象にExporterを導入する
2. クエリを用いてメトリクスの確認
3. grafanaダッシュボードの作成
4. アラートルールの作成
5. 通知を確認。アラート対応
![image](https://user-images.githubusercontent.com/24913906/79315702-4923bc00-7f3e-11ea-8bf7-10d2cb2ab833.png)
