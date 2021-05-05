# 概要

Pushgateway  とは

一言で表現すると、スクレイプが不可能なジョブのメトリクスをプッシュするための仲介サービスです。

研修の中に導入した NodeのCPU使用率などを出力するNode exporterは監視対象が動いている間ずっとメトリクスを吐き続けるため、  
Prometheusで定期的にスクレイプ(pull)することが出来ますが、バッチジョブのような動いている間しか情報を持たないものはスクレイプが出来ないため、通常の方法では監視することが出来ません。

それを解決するため、Pushgatewayを使って、ジョブからのメトリクスのpushを受け付けて永続化し,
Prometheusでこのメトリクスをpullすることでジョブの監視を実現します。

※ 必ずしも Prometheus と同じサーバに pushgateway を導入する必要はありませんが、この研修では Prometheus と同じサーバに pushgateway を導入する構成とします。

# 前準備

## ディレクトリ移動

違うディレクトリへ移動してしまった人は実行する

```
cd /usr/local/src/prometheus
```

# pushgateway のインストール

## 実行ファイルの取得・解凍

```
wget https://github.com/prometheus/pushgateway/releases/download/v1.2.0/pushgateway-1.2.0.linux-amd64.tar.gz
tar -xzf pushgateway-1.2.0.linux-amd64.tar.gz
mv pushgateway-1.2.0.linux-amd64 pushgateway-server
```

## systemd ユニットファイルの作成

1. pushgateway.service ファイルを新規作成・編集する

    ```
    vim /usr/lib/systemd/system/pushgateway.service
    ```

1. 以下の内容で保存する

    `<your_private_ip>` には IaaS サーバー構築で作成した VM のプライベート IP を設定する

    ```
    [Unit]
    Description=Prometheus - Monitoring system and time series database
    Documentation=https://prometheus.io/docs/introduction/overview/
    After=network-online.target

    [Service]
    Type=simple
    ExecStart=/usr/local/src/prometheus/pushgateway-server/pushgateway \
      --web.external-url=http://127.0.0.1/<your_private_ip>/pushgateway/ \
      --web.route-prefix=/ \

    [Install]
    WantedBy=multi-user.target
    ```

## pushgateway.service を有効にして起動

```
systemctl daemon-reload
systemctl enable pushgateway.service
systemctl start pushgateway.service
```

## Web UI の確認

- ブラウザから以下のURLにアクセスする

    ```
    http://<spring_board_fqdn>/<your_private_ip>/pushgateway/
    ```

    ![image](https://user-images.githubusercontent.com/24913906/116718726-3d098000-aa15-11eb-9210-babfca0df6a7.png)

# メトリクスのpushとPrometheusによるスクレイプ

pushgateway を導入したので、実際にメトリクスをpushしてみましょう.
各言語のクライアントを使った叩き方は[ここ](https://prometheus.io/docs/instrumenting/pushing/)にあります。
今回はお手軽に試したので手元の端末のコマンドライン(PowerShell)からpushしてみる.

## メトリクスのPush

1. 以下のコマンドを構築環境より実行する

    ```
    echo "some_metric 3.14" | curl --data-binary @- http://<spring_board_fqdn>/<your_private_ip>/pushgateway//metrics/job/some_job
    ```

1. ブラウザを更新して、登録されているかどうか確認する

    ![image](https://user-images.githubusercontent.com/24913906/116718804-527eaa00-aa15-11eb-8238-1bfd73d2e739.png)

## Prometehusによるスクレイプ

1. prometheus.yml の編集

    ```
    vim /usr/local/src/prometheus/prometheus-server/prometheus.yml
    ```

1. 以下の内容を `scrape_configs` に追記して保存する

    ※\<\> の置換忘れに注意

    ```
    scrape_configs:

     # ....(省略)

     # ここから下を追加
      - job_name: 'pushgateway'
        honor_labels: true
        static_configs:
        - targets: ['localhost:9091'] # Pushgatewayが起動しているポートを指定
    ```

1. Prometheus を再起動

    ```
    systemctl restart prometheus.service
    ```

1. Web UI より Targets が追加されていることを確認

    ![image](https://user-images.githubusercontent.com/24913906/116720460-2ebc6380-aa17-11eb-9743-f74e15788b34.png)

1. メトリクスのPushを構築環境より複数回行う(コマンド発行は時間を空けて実行してください)

    ```
    echo "some_metric 2.71" | curl --data-binary @- http://<spring_board_fqdn>/<your_private_ip>/pushgateway//metrics/job/some_job

    echo "some_metric 1.41" | curl --data-binary @- http://<spring_board_fqdn>/<your_private_ip>/pushgateway//metrics/job/some_job
    ```

1. PromQL の実行

    PromQL を実行してみる

    ```
    some_metric
    ```

    Graph を選択することで時系列で閲覧できる

    ![image](https://user-images.githubusercontent.com/24913906/116721138-f2d5ce00-aa17-11eb-8aa9-b097950a758f.png)

---

<p style="text-align:center"> <a href="./blackbox_exporter_settings"> &lt;- 前へ </a> | <a href="../"> Top </a> | 次へ -&gt; </p>