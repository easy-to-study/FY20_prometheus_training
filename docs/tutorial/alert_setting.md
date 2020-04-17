# 現在の Alerts を確認

1. ブラウザから以下のURLにアクセスする

    ```
    http://<jump_domain>/<your_private_ip>/prometheus/alerts
    ```

2. デフォルトではアラートが設定されてないことを確認
![image](https://user-images.githubusercontent.com/49776559/79524799-61115200-809c-11ea-8474-4d23d37cb5bd.png)

# Alerts を設定する

1. prometheus.yml の編集

    ```
    vim /usr/local/src/prometheus/prometheus-server/prometheus.yml
    ```

    - 変更前

        ```
        # Alertmanager configuration
        alerting:
          alertmanagers:
          - static_configs:
            - targets:
              # - alertmanager:9093
        
        # Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
        rule_files:
          # - "first_rules.yml"
          # - "second_rules.yml"
        ```

    - 変更後

        ```
        # Alertmanager configuration
        alerting:
          alertmanagers:
          - static_configs:
            - targets:
              - localhost:9093
        
        # Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
        rule_files:
          - "alert_rules.yml"
        ```

2. alert_rules.yml の作成

    ```
    vim /usr/local/src/prometheus/prometheus-server/alert_rules.yml
    ```

    - 新規作成

        ```
        groups:
          - name: basic_alert_rule
            rules:
            - alert: InstanceDown
              expr: up == 0
              for: 2m
              labels:
                severity: critical
              annotations:
                summary: "Instance {{ $labels.instance }} down"
                description: "{{ $labels.instance }} of job {{ $labels.job }} has been down for more than 2 minutes."
        
            - alert: HostHighCpuLoad
              expr: 100 - (avg by(instance) (irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 80
              for: 2m
              labels:
                severity: warning
              annotations:
                summary: "Host high CPU load (instance {{ $labels.instance }})"
                description: "CPU load is > 80%\n  VALUE = {{ $value }}\n  LABELS: {{ $labels }}"
        
            - alert: HostOutOfMemory
              expr: node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes * 100 < 10
              for: 2m
              labels:
                severity: warning
              annotations:
                summary: "Host out of memory (instance {{ $labels.instance }})"
                description: "Node memory is filling up (< 10% left)\n  VALUE = {{ $value }}\n  LABELS: {{ $labels }}"
        
            - alert: HostOutOfDiskSpace
              expr: (node_filesystem_avail_bytes{device="rootfs"}  * 100) / node_filesystem_size_bytes{device="rootfs"} < 10
              for: 2m
              labels:
                severity: warning
              annotations:
                summary: "Host out of disk space (instance {{ $labels.instance }})"
                description: "Disk is almost full (< 10% left)\n  VALUE = {{ $value }}\n  LABELS: {{ $labels }}"
        ```

3. Prometheus を再起動

    ```
    systemctl restart prometheus.service
    ```

4. Web UI より Alerts を選択して、現在のアラートを確認する

    ![image](https://user-images.githubusercontent.com/49776559/79524800-61a9e880-809c-11ea-9cd0-b1334f81b503.png)


# メール通知を設定する

1. alertmanager.yml の編集

    ```
    vim /usr/local/src/prometheus/alertmanager-server/alertmanager.yml
    ```

    - 変更前

        ```
        global:
        resolve_timeout: 5m

        route:
        group_by: ['alertname']
        group_wait: 10s
        group_interval: 10s
        repeat_interval: 1h
        receiver: 'web.hook'
        receivers:
        - name: 'web.hook'
        webhook_configs:
        - url: 'http://127.0.0.1:5001/'
        inhibit_rules:
        - source_match:
            severity: 'critical'
            target_match:
            severity: 'warning'
            equal: ['alertname', 'dev', 'instance']
        ```

    - 変更後

        ```
        global:
          smtp_smarthost: 'smtp.sendgrid.net:587'
          smtp_from: 'alertmanager@tech.softbank.co.jp'
          smtp_auth_username: '<smtp_auth_username>'
          smtp_auth_password: '<smtp_auth_password>'
        
        
        route:
          receiver: "mail"
          group_by: ['alertname', 'instance', 'severity']
          group_wait: 30s
          group_interval: 5m
          repeat_interval: 1h
        
        receivers:
         - name: 'mail'
           email_configs:
           - to: '<alias>@tech.softbank.co.jp'
        ```

2. alertmanager を再起動

    ```
    systemctl restart alertmanager.service
    ```

# アラート確認

1. Web UI より Alerts を選択して、アラートのステータス変化を確認する

    - 異常検知

        ![image](https://user-images.githubusercontent.com/49776559/79524801-62427f00-809c-11ea-893a-efa52fa46fe0.png)

    - alertmanagerへ通知

        ![image](https://user-images.githubusercontent.com/49776559/79524802-62427f00-809c-11ea-93ca-a670a5687815.png)

2. Outlookを起動して、メールを着信できたことを確認する

    - メール通知
    
        ![image](https://user-images.githubusercontent.com/49776559/79524805-62db1580-809c-11ea-9b82-729c32fffc93.png)
        
# Appendix

[Configuration alerting rules \| Prometheus](https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/)

---

<p style="text-align:center"> <a href="./grafana_settings"><- 前へ </a> | <a href="../"> Top </a> |次へ-> </p>
