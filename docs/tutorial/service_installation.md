# 前準備

## ディレクトリ作成・移動

```
mkdir /usr/local/src/prometheus
cd /usr/local/src/prometheus
```

# Prometheus のインストール

## 実行ファイルの取得・解凍

```
wget https://github.com/prometheus/prometheus/releases/download/v2.17.1/prometheus-2.17.1.linux-amd64.tar.gz
tar zxvf prometheus-2.17.1.linux-amd64.tar.gz
mv prometheus-2.17.1.linux-amd64 prometheus-server
```

## systemd ユニットファイルの作成

1. prometheus.service ファイルを新規作成・編集する

    ```
    vim /usr/lib/systemd/system/prometheus.service
    ```

2. 以下の内容で保存する

    `<your_private_ip>` には前のページで作成した VM のプライベートIPを設定する
    ```
    [Unit]
    Description=Prometheus - Monitoring system and time series database
    Documentation=https://prometheus.io/docs/introduction/overview/
    After=network-online.target

    [Service]
    Type=simple
    ExecStart=/usr/local/src/prometheus/prometheus-server/prometheus \
      --config.file=/usr/local/src/prometheus/prometheus-server/prometheus.yml \
      --web.external-url=http://127.0.0.1/<your_private_ip>/prometheus/ \
      --web.route-prefix=/ \

    [Install]
    WantedBy=multi-user.target
    ```

    - vim tips
      - ノーマルモード
        - 方向キー または `hjkl` キーを入力することでカーソル移動
        - `i` キーを入力すると 入力モード へ
        - `:` を入力すると コマンドラインモード へ
      - 入力モード
        - 方向キーでカーソル移動
        - 文字列の入力、編集
      - コマンドラインモード
        - `:wq` と入力すると保存して閉じる
      - 共通
        - `Esc` キーを入力すると ノーマルモード へ
      - Appendix
        - vim がインストールされている環境であれば `vimtutor ja` コマンドにて基本操作を学習できるため、暇なときにやっておくと〇

## prometheus.service を有効にして起動

```
systemctl daemon-reload
systemctl enable prometheus.service
systemctl start prometheus.service
```

## Web UI の確認

- ブラウザから以下のURLにアクセスする

    ```
    http://<spring_board_fqdn>/<your_private_ip>/prometheus/
    ```

    ![image](https://user-images.githubusercontent.com/63433549/79302922-985ef200-7f28-11ea-9b36-49a0292133c9.png)

# Grafana のインストール

## Grafana のパッケージ取得・インストール

```
wget https://dl.grafana.com/oss/release/grafana-6.7.2-1.x86_64.rpm
sudo yum install grafana-6.7.2-1.x86_64.rpm
```

## grafana.ini の編集

1. grafana.ini を編集する

    ```
    vim /etc/grafana/grafana.ini
    ```

2. root_url, serve_from_sub_path を設定する

    今回変更するのは `;root_url`, `;serve_from_sub_path` から始まる2行

    - 変更前

        ```
        # The full public facing url you use in browser, used for redirects and emails
        # If you use reverse proxy and sub path specify full url (with sub path)
        ;root_url = %(protocol)s://%(domain)s:%(http_port)s/

        # Serve Grafana from subpath specified in `root_url` setting. By default it is set to `false` for compatibility reasons.
        ;serve_from_sub_path = false
        ```

    - 変更後

        `<your_private_ip>` の変更漏れに注意
        ```
        # The full public facing url you use in browser, used for redirects and emails
        # If you use reverse proxy and sub path specify full url (with sub path)
        root_url = %(protocol)s://%(domain)s:%(http_port)s/<your_private_ip>/grafana/

        # Serve Grafana from subpath specified in `root_url` setting. By default it is set to `false` for compatibility reasons.
        serve_from_sub_path = true
        ```

    - vim tips
      - ノーマルモード
        - `/` を入力すると 検索モード へ
        - `x` を入力すると現在のカーソル位置の1文字を削除する
        - `$` を入力すると行末へカーソル移動
        - `a` を入力すると現在のカーソル位置の1つ後ろで 入力モード へ
      - 検索モード
        - `/;root_url`, `/;serve_from_sub_path` を入力して Enter キーを押下するとカーソル移動する

## grafana-server.service を有効にして起動

```
systemctl enable grafana-server.service
systemctl start grafana-server.service
```

## Web UI の確認

- ブラウザから以下のURLにアクセスする

    ```
    http://<spring_board_fqdn>/<your_private_ip>/grafana/
    ```

    ![image](https://user-images.githubusercontent.com/63433549/79311201-09f26c80-7f38-11ea-81e2-e2fe6028ce2a.png)

# AlertManager のインストール

## 実行ファイルの取得・解凍

```
wget https://github.com/prometheus/alertmanager/releases/download/v0.20.0/alertmanager-0.20.0.linux-amd64.tar.gz
tar xzvf alertmanager-0.20.0.linux-amd64.tar.gz
mv alertmanager-0.20.0.linux-amd64 alertmanager-server
```

## systemd ユニットファイルの作成

1. alertmanager.service ファイルを新規作成・編集する

    ```
    vim /usr/lib/systemd/system/alertmanager.service
    ```

2. 以下の内容で保存する

    ```
    [Unit]
    Description=AlertManager

    [Service]
    Type=simple
    ExecStart=/usr/local/src/prometheus/alertmanager-server/alertmanager \
      --config.file=/usr/local/src/prometheus/alertmanager-server/alertmanager.yml \
      --cluster.advertise-address 127.0.0.1:9093 \
      --web.external-url=http://127.0.0.1/<your_private_ip>/alertmanager/ \
      --web.route-prefix=/ \

    [Install]
    WantedBy=default.target
    ```

    - vim tips
      - コマンドラインモード
        - 置換する場合は `:%s/<your_private_ip>/<置換後の文字列>/g`

## alertmanager.service を有効にして起動

```
systemctl daemon-reload
systemctl enable alertmanager.service
systemctl start alertmanager.service
```

## Web UI の確認

- ブラウザから以下のURLにアクセスする

    ```
    http://<spring_board_fqdn>/<your_private_ip>/alertmanager/
    ```

    ![image](https://user-images.githubusercontent.com/63433549/79322134-5b562800-7f47-11ea-8154-36d3aaac0ff8.png)

---

<p style="text-align:center"> <a href="./iaas_building"> <- 前へ </a> | <a href="../"> Top </a> | <a href="./prometheus_settings"> 次へ-> </a> </p>
