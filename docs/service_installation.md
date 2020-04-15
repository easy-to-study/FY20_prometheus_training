# �O����

## �f�B���N�g���쐬�E�ړ�

```
mkdir /usr/local/src/prometheus
cd /usr/local/src/prometheus
```

# Prometheus �̃C���X�g�[��

## Prometheus �_�E�����[�h�E��

```
wget https://github.com/prometheus/prometheus/releases/download/v2.17.1/prometheus-2.17.1.linux-amd64.tar.gz
tar zxvf prometheus-2.17.1.linux-amd64.tar.gz
mv prometheus-2.17.1.linux-amd64 prometheus-server
```

## systemd ���j�b�g�t�@�C���̍쐬

1. prometheus.service �t�@�C����V�K�쐬�E�ҏW����

```
vim /usr/lib/systemd/system/prometheus.service
```

2. �ȉ��̓��e�ŕۑ�����

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

## service ��L���ɂ��ċN��

```
systemctl daemon-reload
systemctl enable prometheus.service
systemctl start prometheus.service
```

## Web UI �̊m�F

1. �u���E�U����ȉ���URL�ɃA�N�Z�X����

http://fy20-springboard.japaneast.cloudapp.azure.com/<your-private-ip>/prometheus/

![image](https://user-images.githubusercontent.com/63433549/79302922-985ef200-7f28-11ea-9b36-49a0292133c9.png)
