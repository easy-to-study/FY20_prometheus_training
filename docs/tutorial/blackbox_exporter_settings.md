# 概要

blackbox_exporter とは

今まで exporter は監視対象のサーバに導入してきました。  
上記の手法では exporter を導入していないサーバに対して ICMP (ping) による監視や、HTTP 監視はできません。  
blackbox_exporter は exporter を導入していないサーバの死活監視を実現します。

必ずしも Prometheus と同じサーバに blackbox_exporter を導入する必要はありませんが、この研修では Prometheus と同じサーバに blackbox_exporter を導入する構成とします。

# 前準備

## ディレクトリ移動

違うディレクトリへ移動してしまった人は実行する
```
cd /usr/local/src/prometheus
```

# blackbox_exporter のインストール

## 実行ファイルの取得・解凍

```
wget https://github.com/prometheus/blackbox_exporter/releases/download/v0.18.0/blackbox_exporter-0.18.0.linux-amd64.tar.gz
tar zxvf blackbox_exporter-0.18.0.linux-amd64.tar.gz
mv blackbox_exporter-0.18.0.linux-amd64 blackbox_exporter-server
```

## systemd ユニットファイルの作成

1. blackbox_exporter.service ファイルを新規作成・編集する

    ```
    vim /usr/lib/systemd/system/blackbox_exporter.service
    ```

1. 以下の内容で保存する

    `<your_private_ip>` には IaaS サーバー構築で作成した VM のプライベート IP を設定する
    ```
    [Unit]
    Description=blackbox_exporter
    Documentation=https://github.com/prometheus/blackbox_exporter

    [Service]
    Type=simple
    ExecStart=/usr/local/src/prometheus/blackbox_exporter-server/blackbox_exporter \
      --config.file=/usr/local/src/prometheus/blackbox_exporter-server/blackbox.yml \
      --web.external-url=http://127.0.0.1/<your_private_ip>/blackbox_exporter/ \
      --web.route-prefix=/ \

    [Install]
    WantedBy=multi-user.target
    ```

## blackbox_exporter.service を有効にして起動

```
systemctl daemon-reload
systemctl enable blackbox_exporter.service
systemctl start blackbox_exporter.service
```

## Web UI の確認

- ブラウザから以下のURLにアクセスする

    ```
    http://<spring_board_fqdn>/<your_private_ip>/blackbox_exporter/
    ```

    ![image](https://user-images.githubusercontent.com/63433549/116691570-ee002280-a9f5-11eb-954a-24632fb75ff4.png)

# 死活監視の設定

blackbox_exporter を導入したので、実際に Prometheus から外部のサーバを死活監視してみます

## ICMP 監視

1. prometheus.yml の編集

    ```
    vim /usr/local/src/prometheus/prometheus-server/prometheus.yml
    ```

1. 以下の内容を `scrape_configs` に追記して保存する

    ※\<\> の置換忘れに注意

    変更前
    ```
    scrape_configs:
      # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
      - job_name: 'prometheus'

        # metrics_path defaults to '/metrics'
        # scheme defaults to 'http'.

        static_configs:
        - targets: ['<node_exporter_private_ip>:9100']
    ```

    変更後
    ```
    scrape_configs:
      # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
      - job_name: 'prometheus'

        # metrics_path defaults to '/metrics'
        # scheme defaults to 'http'.

        static_configs:
        - targets: ['<node_exporter_private_ip>:9100']
      - job_name: 'blackbox-icmp'
        metrics_path: /probe
        params:
          module: [icmp]
        static_configs:
          - targets:
            - <node_exporter_private_ip>
        relabel_configs:
          - source_labels: [__address__]
            target_label: __param_target
          - source_labels: [__param_target]
            target_label: instance
          - target_label: __address__
            replacement: localhost:9115 # blackbox_exporter
    ```

1. Prometheus を再起動

    ```
    systemctl restart prometheus.service
    ```

1. Web UI より Targets が追加されていることを確認

    ![image](https://user-images.githubusercontent.com/63433549/116698643-8cdd4c80-a9ff-11eb-80e5-f7d3f27099c8.png)

1. PromQL の実行

    PromQL を実行してみる
    ```
    probe_success{instance="<node_exporter_private_ip>", job="blackbox-icmp"}
    ```

    Graph を選択することで時系列で閲覧できる
    ```
    UP: 1
    DONW: 0
    ```

    ![image](https://user-images.githubusercontent.com/63433549/116699360-7683c080-aa00-11eb-97ab-10f50d58f1ca.png)

## HTTP 監視

1. blackbox_exporter の設定変更

    ```
    vim /usr/local/src/prometheus/blackbox_exporter-server/blackbox.yml
    ```

1. `http_2xx` を以下の内容に変更して保存する

    ```
      http_2xx:
        prober: http
        timeout: 15s
        http:
          valid_status_codes: []
          method: GET
          preferred_ip_protocol: "ip4"
    ```

1. blackbox_exporter を再起動

    ```
    systemctl restart blackbox_exporter.service
    ```

1. prometheus.yml の編集

    ```
    vim /usr/local/src/prometheus/prometheus-server/prometheus.yml
    ```

1. 以下の内容を `scrape_configs` に追記して保存する

    ※\<\> の置換忘れに注意

    ```
      - job_name: 'blackbox-http'
        scrape_timeout: 15s
        metrics_path: /probe
        params:
          module: [http_2xx]
        static_configs:
          - targets:
            - <favorite_websites> # example: https://www.google.com/
            labels:
              group: 'response'
        relabel_configs:
          - source_labels: [__address__]
            target_label: __param_target
          - source_labels: [__param_target]
            target_label: instance
          - target_label: __address__
            replacement: localhost:9115 # blackbox_exporter
    ```

1. Prometheus を再起動

    ```
    systemctl restart prometheus.service
    ```

1. Web UI より Targets が追加されていることを確認

    ![image](https://user-images.githubusercontent.com/63433549/116701932-70dbaa00-aa03-11eb-9ae9-c9e5e0ab01ef.png)

1. PromQL の実行

    PromQL を実行してみる
    ```
    probe_success{instance="<favorite_websites>", job="blackbox-http"}
    ```

    ![image](https://user-images.githubusercontent.com/63433549/116702136-a8e2ed00-aa03-11eb-8b87-af0a381571ad.png)

# Appendix

[prometheus/blackbox_exporter](https://github.com/prometheus/blackbox_exporter)

---

<p style="text-align:center"> <a href="./alert_setting"> &lt;- 前へ </a> | <a href="../"> Top </a> | <a href="./pushgateway_exporter_settings"> 次へ -&gt; </a> </p>
