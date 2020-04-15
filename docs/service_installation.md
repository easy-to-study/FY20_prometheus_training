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

## service を有効にして起動

```
systemctl daemon-reload
systemctl enable prometheus.service
systemctl start prometheus.service
```

## Web UI の確認

1. ブラウザから以下のURLにアクセスする

http://fy20-springboard.japaneast.cloudapp.azure.com/<your-private-ip>/prometheus/

![image](https://user-images.githubusercontent.com/63433549/79302922-985ef200-7f28-11ea-9b36-49a0292133c9.png)
