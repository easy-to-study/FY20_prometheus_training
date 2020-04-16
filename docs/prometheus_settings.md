# 現在の Targets を確認

- Web UI より Status → Targets と選択して現在の Targets を確認する

    デフォルトでは `http://localhost:9090/metrics` エンドポイントが設定されている
    ![image](https://user-images.githubusercontent.com/63433549/79405722-2510bb00-7fd0-11ea-8e1e-004415e7a81e.png)

# Targets を変更する

1. prometheus.yml の編集

    ```
    vim /usr/local/src/prometheus/prometheus-server/prometheus.yml
    ```

    - 変更前

        ```
        # A scrape configuration containing exactly one endpoint to scrape:
        # Here it's Prometheus itself.
        scrape_configs:
          # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
          - job_name: 'prometheus'

            # metrics_path defaults to '/metrics'
            # scheme defaults to 'http'.

            static_configs:
            - targets: ['localhost:9090']
        ```

    - 変更後

        ```
        # A scrape configuration containing exactly one endpoint to scrape:
        # Here it's Prometheus itself.
        scrape_configs:
          # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
          - job_name: 'prometheus'

            # metrics_path defaults to '/metrics'
            # scheme defaults to 'http'.

            static_configs:
            - targets: ['<node_exporter_ip>:9100']
        ```

2. Prometheus を再起動

    ```
    systemctl restart prometheus.service
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

[Querying basics \| Prometheus](https://prometheus.io/docs/prometheus/latest/querying/basics/)
