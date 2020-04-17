# 現在の Alerts を確認

- Web UI より Alerts を選択して現在の Alerts を確認する

    デフォルトではアラートが設定されていない
    ![image](https://user-images.githubusercontent.com/63433549/79405722-2510bb00-7fd0-11ea-8e1e-004415e7a81e.png)

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
              - alertmanager:9093
        
        # Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
        rule_files:
          - "alert_rules.yml"
        ```

2. alert_rules.yml の編集

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
                description: "{{ $labels.instance }} of job {{ $labels.job }} has been down for more than 5 minutes."
        
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

4. Web UI より Targets が変更されていることを確認

    ![image](https://user-images.githubusercontent.com/63433549/79409883-f26bc000-7fd9-11ea-9aff-58ebc7f665a2.png)


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
          smtp_auth_username: 'apikey'
          smtp_auth_password: 'SG.R1spG2zSQDWbi4NZwZmVyA.4ZGHswzipG1Gd6OGvO2Hn9w00a22BA1PZCAZCAPfhhc'
        
        
        route:
          receiver: "mail"
          group_by: ['alertname', 'instance', 'severity']
          group_wait: 30s
          group_interval: 5m
          repeat_interval: 1h
        
        receivers:
         - name: 'mail'
           email_configs:
           - to: '<送信先メールアドレス>'
        ```

2. alertmanager を再起動

    ```
    systemctl restart alertmanager.service
    ```

3. Web UI より Targets が変更されていることを確認

    ![image](https://user-images.githubusercontent.com/63433549/79409883-f26bc000-7fd9-11ea-9aff-58ebc7f665a2.png)

# Prom QL の実行

- Web UI より Graph を選択して任意の Prom QL を実行する

    - CPU 使用率

        ```
        100 - (avg(irate(node_cpu_seconds_total{mode="idle"}[30m])) * 100)
        ```

    - Memory 使用率

        ```
        (node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes
        ```

    - Disk 空き容量

        ```
        node_filesystem_free_bytes{fstype=~"ext4|xfs"}
        ```

    - 実行イメージ

        ![image](https://user-images.githubusercontent.com/63433549/79417098-10dab700-7fec-11ea-9dc0-8edcf97f9daa.png)

# Appendix

[Configuration alerting rules \| Prometheus](https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/)
