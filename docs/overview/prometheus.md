# Prometheusとは

![image](https://user-images.githubusercontent.com/24913906/79299452-0b636b00-7f1f-11ea-8c31-0fe2e93da8f0.png)

サーバのリソース状況やソフトウェアの統計情報といった各種メトリクスを収集して監視を行うモニタリングシステムです。

ロギングやトレーシングを行うといった多用途のものではなくメトリクス収集に特化しているモニタリングシステムで、人気が高まりつつあるモニタリングシステムです。

## Prometheusのすごみ

- Twitterのフォロワー数・伸びがすごい

|            | 2019/06 | 2020/04 |
| ---------- | ------- | ------- |
| prometheus | 20000   | 28000   |
| zabbix     | 7000    | 7700    |
| nagios     | 5000    | 5900    |

- 他に思いついたら記載する

<!-- CNCF、OSSの話を盛り込むか悩み中 -->

## 専門用語

| 用語      | 意味                          | 備考                              |
| ------- | --------------------------- | ------------------------------- |
| 時系列データ  | 時間的に変化した情報を持つデータのことを言います。 | 気温の遷移や降水状況などの気象観測など、ある一時期の状態を表す |
| メトリクス   | 基準とすべき尺度や指標のことを言います。        | HTTPリクエストの総数を表します。              |
| スクレイピング | メトリクスの収集することを言います。          | 収集周期は任意に設定可能                    |
| インスタンス  | スクレイピングができるエンドポイントのことを指します  | わかりやすくいえば監視対象                   |
| ジョブ     | 同じ目的を持つインスタンスの集まりのことを指します   | 少しわかりづらいので以下に例を記載します            |

```plain
例：4つのインスタンスを持つAPIサーバージョブ
ジョブ： api-server
    インスタンス1： 1.2.3.4:5670
    インスタンス2： 1.2.3.4:5671
    インスタンス3： 5.6.7.8:5670
    インスタンス4： 5.6.7.8:5671
```

## 特徴

- オープンソース(OSS)
- バイナリを動かすだけで動作する
  - Golang製のため、言語環境の整備・プラグインが不要
  - 依存システムがない
- ホストが増減する前提で設計されている
  - オートスケーリング、ローリングアップデートによるホスト名・IPアドレスの変更に対応できる
- 柔軟な可視化
  - 様々なグラフ化とダッシュボードをサポート
- アラートを減らす思想
  - アラートのグルーピング・ミュート機能をサポート

## アーキテクチャ

Prometheus は複数のコンポーネントで構成されています。

![image](https://user-images.githubusercontent.com/24913906/79299342-c0495800-7f1e-11ea-89cd-a7c222d36b50.png)

> 引用: [Overview_Prometheus](https://prometheus.io/docs/introduction/overview/)

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

    | 種類         | 説明                             |
    | ---------- | ------------------------------ |
    | Azure      | Azure上のVMのディスカバリ               |
    | ec2        | Amaozn EC2インスタンス向けのディスカバリ      |
    | gce        | Google Cloud Platform向けのディスカバリ |
    | kubernetes | kubernetes向けのディスカバリ            |

### コンポーネントのまとめ

![image](https://user-images.githubusercontent.com/24913906/79300874-f688d680-7f22-11ea-951e-d35b0ba58784.png)

## Prometheusの運用イメージ

1. 監視対象にExporterを導入
2. クエリを用いてメトリクス取得できているか確認
3. grafanaダッシュボードの作成
4. prometheusのconfig設定
5. alertmanagerのconfig設定
6. 通知を確認

![image](https://user-images.githubusercontent.com/24913906/79315702-4923bc00-7f3e-11ea-8bf7-10d2cb2ab833.png)

---

<p style="text-align:center"> <a href="http://hoge"><- 前へ </a> | <a href="http://hoge"> Top </a> |<a href="http://hoge">次へ-> </a></p>
