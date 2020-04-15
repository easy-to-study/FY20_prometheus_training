# 前準備

## ディレクトリ作成・移動

```
mkdir /usr/local/src/prometheus
cd /usr/local/src/prometheus
```

# Prometheus のインストール

## Prometheus ダウンロード・解凍

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

```
[Unit]
Description=Prometheus - Monitoring system and time series database
Documentation=https://prometheus.io/docs/introduction/overview/
After=network-online.target

[Service]
Type=simple
ExecStart=/usr/local/src/prometheus/prometheus-server/prometheus \
  --config.file=/usr/local/src/prometheus/prometheus-server/prometheus.yml \
  --web.external-url=http://127.0.0.1/<your-private-ip>/prometheus/ \
  --web.route-prefix=/ \

[Install]
WantedBy=multi-user.target
```

## prometheus.service を有効にして起動

```
systemctl daemon-reload
systemctl enable prometheus.service
systemctl start prometheus.service
```

## Web UI の確認

- ブラウザから以下のURLにアクセスする

http://fy20-springboard.japaneast.cloudapp.azure.com/<your-private-ip>/prometheus/

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

1. root_url, serve_from_sub_path を設定する

変更前

```
# The full public facing url you use in browser, used for redirects and emails
# If you use reverse proxy and sub path specify full url (with sub path)
;root_url = %(protocol)s://%(domain)s:%(http_port)s/

# Serve Grafana from subpath specified in `root_url` setting. By default it is set to `false` for compatibility reasons.
;serve_from_sub_path = false
```

変更後

```
# The full public facing url you use in browser, used for redirects and emails
# If you use reverse proxy and sub path specify full url (with sub path)
root_url = %(protocol)s://%(domain)s:%(http_port)s/<your-private-ip>/grafana/

# Serve Grafana from subpath specified in `root_url` setting. By default it is set to `false` for compatibility reasons.
serve_from_sub_path = true
```

## grafana-server.service を有効にして起動

```
systemctl enable grafana-server.service
systemctl start grafana-server.service
```

## Web UI の確認

- ブラウザから以下のURLにアクセスする

http://fy20-springboard.japaneast.cloudapp.azure.com/<your-private-ip>/grafana/

![image](https://user-images.githubusercontent.com/63433549/79311201-09f26c80-7f38-11ea-81e2-e2fe6028ce2a.png)
