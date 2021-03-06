# Prometheusとは

![image](https://user-images.githubusercontent.com/24913906/79299452-0b636b00-7f1f-11ea-8c31-0fe2e93da8f0.png)

サーバのリソース状況などの様々なメトリクスを収集して監視を行うモニタリングシステムです。

ロギングやトレーシングを行うといった多用途のものではなくメトリクス収集に特化しているモニタリングシステムで、人気が高まりつつあるモニタリングシステムです。

## Prometheusのすごみ

- オープンソース(OSS)なので無料で利用できてすごい
- Golang製のためバイナリを動かすだけで動作してすごい
- 大企業でガンガン採用されていてすごい
  - [Cookpad and Prometheus - YouTube](https://www.youtube.com/watch?v=Ik1pvqVTC0w)
  - [Yahoo!JAPAN データセンターネットワークでPrometheus活用事例 - YouTube](https://www.youtube.com/watch?v=cCQO6KCvUzA)
- 書籍がわかりやすくてすごい
  - [O'Reilly Japan - 入門 Prometheus](https://www.oreilly.co.jp/books/9784873118772/)
- Twitterのフォロワー数・伸びがすごい

|            | 2019/06 | 2020/04 |
| ---------- | ------- | ------- |
| prometheus | 20000   | 28000   |
| zabbix     | 7000    | 7700    |
| nagios     | 5000    | 5900    |

- [プロダクショングレードのコンテナ管理基盤 - Kubernetes](https://kubernetes.io/ja/)に対応できてすごい
- アラートを減らす思想が画期的ですごい

## 専門用語

| 用語      | 意味                          | 備考                              |
| ------- | --------------------------- | ------------------------------- |
| 時系列データ  | 時間的に変化した情報を持つデータ| 気温の遷移や降水状況などの気象観測など、ある一時期の状態を表す |
| メトリクス   | 基準とすべき尺度や指標のこと| CPU,Memoryの使用率やHTTPリクエストの総数などを指す              |
| スクレイピング | HTTPリクエストを用いてメトリクスを取得すること|                     |

## アーキテクチャ

Prometheus は複数のコンポーネントで構成されています。

![image](https://user-images.githubusercontent.com/24913906/79299342-c0495800-7f1e-11ea-89cd-a7c222d36b50.png)

> 引用: [Overview_Prometheus](https://prometheus.io/docs/introduction/overview/)

## コンポーネント

### Prometheus server

- 監視サーバーのプログラム
- 定期的に監視対象サーバー(targets)に対してスクレイピングする
- 取得したデータは prometheus 内の DB に保持される
- アラートの計算と発火を行う

### Exporter

- 監視対象サーバー上で動かすプログラム
- テキスト形式でリソース情報を公開するWeb API のようなもの
- 監視対象のリソース毎に exporter が用意されている
  - ない場合、Clientライブラリが提供されている言語であれば比較的簡単に自作することができる
    - 対象言語：go, python,java,ruby [Exporters and integrations_Prometheus](https://prometheus.io/docs/instrumenting/exporters/#other-third-party-utilities)

### grafana

- ログ・データ可視化のためのツールで、Prometheus で取得したデータをカッコよく可視化する(ダッシュボード)ために利用されます。

### Alertmanager

- Prometheus Server からのアラート・イベントを受け取り、定義された通知設定 (例えばE-mail、slack 等) に基づいてアラートを送信します。
- ミュート・グルーピング機能を用いることでアラート件数を減らすことも可能

### Pushgateway

- 主には一時的なジョブのプッシュに使用されます。
- このタイプのジョブは存在する期間が短く、PrometheusがPullを行えば消えるかもしれないため、ゲートウェイを使ってプッシュします。

### Service discovery

- 監視対象を自動的に見つけることができる
- サポートしている主なプラットフォームは以下が挙げられる

    | 種類         | 説明                             |
    | ---------- | ------------------------------ |
    | Azure      | Azure上のVMのディスカバリ               |
    | ec2        | Amaozn EC2インスタンス向けのディスカバリ      |
    | gce        | Google Cloud Platform向けのディスカバリ |
    | kubernetes | kubernetes向けのディスカバリ            |

    公式リンク: [Configuration_Prometheus](https://prometheus.io/docs/prometheus/latest/configuration/configuration/)

### コンポーネントのまとめ

![image](https://user-images.githubusercontent.com/24913906/79300874-f688d680-7f22-11ea-951e-d35b0ba58784.png)


## まとめ

Promehteusはエコシステムなアーキテクチャ、時系列データ/クエリを用いた柔軟な監視設定・ダッシュボードや
アラート数を抑制できるなど、多くのすばらしい機能があります。  

つづいては、データの取得方法について学んでいきましょう！

---

<p style="text-align:center"> <a href="../overview"> &lt;- 前へ </a> | <a href="../"> Top </a> | <a href="./promql"> 次へ -&gt; </a> </p>
