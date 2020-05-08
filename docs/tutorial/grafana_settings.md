# Grafana Web UI にログイン

1. ブラウザから以下のURLにアクセスする

    ```
    http://<spring_board_fqdn>/<your_private_ip>/grafana/
    ```

2. Grafana へ最初にアクセスした場合、ログイン画面が表示されるため admin / admin でログインする

    ![image](https://user-images.githubusercontent.com/63433549/79427916-628d3c80-8000-11ea-86fc-c04b3ddcdca1.png)

3. パスワード変更画面へ遷移するが、今回は Skip を押下する

    ![image](https://user-images.githubusercontent.com/63433549/79428463-25757a00-8001-11ea-824a-57f635c21d90.png)

# Data Source を追加する

1. サイドメニューから歯車のアイコン → Data Sources を押下する

    ![image](https://user-images.githubusercontent.com/63433549/79428843-b3e9fb80-8001-11ea-8055-2e06396d8558.png)

2. Add data source を押下する

    ![image](https://user-images.githubusercontent.com/63433549/79429127-15aa6580-8002-11ea-9817-5e4fa2733364.png)

3. 今回は Prometheus を使用するため、Prometheus → Select を押下する

    ![image](https://user-images.githubusercontent.com/63433549/79429426-746fdf00-8002-11ea-8b43-9265412c7658.png)

4. 以下の内容で Data Source を追加する

    ```
    - HTTP
        - URL: http://localhost:9090
    ```

    今回は Prometheus と Grafana を同じサーバに配置しているため、localhost:9090 を指定している
    ![image](https://user-images.githubusercontent.com/63433549/79429978-345d2c00-8003-11ea-88d4-bc9ae65394c5.png)

5. Save & Test を押下してエラーが表示されなければ成功

# Grafana から PromQL を実行してみる

1. サイドメニューから方位磁石みたいなアイコン → Explore を押下する

    ![image](https://user-images.githubusercontent.com/63433549/79431077-e47f6480-8004-11ea-8432-9c0700a5b427.png)

2. 任意の PromQL を入力して Run Query を押下 (Enter でも可)

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

    - Sample

        Prometheus の Web UI と同様に PromQL が実行できることを確認
        ![image](https://user-images.githubusercontent.com/63433549/79431528-88691000-8005-11ea-97e6-89613189efe9.png)

# ダッシュボードを作成してみる

1. サイドメニューから + のアイコン → Dashboard を押下する

    ![image](https://user-images.githubusercontent.com/63433549/79518298-b3964280-808b-11ea-83ae-8900bfc14be0.png)

2. ダッシュボードの作成画面へ遷移したら Add Query を押下する

    ![image](https://user-images.githubusercontent.com/63433549/79518471-21db0500-808c-11ea-9d6c-badc60986a0d.png)

3. Queries を選択して Query A に任意の PromQL を入力する

    今まで手動で実行していた PromQL がダッシュボードで可視化できるようになった
    ![image](https://user-images.githubusercontent.com/63433549/79518754-d543f980-808c-11ea-85ca-429329e6ffc0.png)

4. パネルの設定例

    - Queries で Add Query を押下すると1つのパネルで複数のグラフを表示することもできる

        ![image](https://user-images.githubusercontent.com/63433549/79528841-04b33000-80a6-11ea-93e0-1b67848971c3.png)

    - Visualization からグラフの種類を変更できる

        ![image](https://user-images.githubusercontent.com/63433549/79522442-b21e4780-8096-11ea-8202-2e5ba0253583.png)

    - General からパネルのタイトルを変更できる

        ![image](https://user-images.githubusercontent.com/63433549/79528598-5d35fd80-80a5-11ea-8c5f-8372e8f0d31e.png)

5. パネルの編集が完了したら ← を押下するか Esc を入力するとダッシュボード画面まで戻ることができる

    ![image](https://user-images.githubusercontent.com/63433549/79522862-c7e03c80-8097-11ea-8d86-945e000c7eab.png)

6. 必要なパネルが揃ったら保存してみる

    画面上部のフロッピーディスクのアイコンから保存できる
    ![image](https://user-images.githubusercontent.com/63433549/79522995-1e4d7b00-8098-11ea-9cfb-1638c5098423.png)

    ダイアログが表示されるので、任意の名前を入力して Save を押下する
    ![image](https://user-images.githubusercontent.com/63433549/79523258-aa5fa280-8098-11ea-8074-30b4f052b160.png)

7. 保存完了

    次回からは Dashboards の Home や Manage から保存したダッシュボードを参照できる
    ![image](https://user-images.githubusercontent.com/63433549/79523418-0de9d000-8099-11ea-955b-a234fed68a77.png)

# テンプレートをインポートしてみる

1. Create の Import を押下する

    ![image](https://user-images.githubusercontent.com/63433549/79523636-9d8f7e80-8099-11ea-9044-e3d450bc2809.png)

2. テンプレートを指定する

    Grafana.com の URL, ID のいずれかを入力するか、json 文字列を入力することでテンプレートを指定できる

    今回は Grafana.com Dashboard の欄に `11952` と入力する。テンプレートは [公式ページの Dashboards](https://grafana.com/grafana/dashboards?orderBy=name&direction=asc) から検索できる。
    ![image](https://user-images.githubusercontent.com/63433549/79523834-fe1ebb80-8099-11ea-9fec-fb6cc5fe1639.png)

3. インポートするための情報を入力して Import を押下する

    今回は Options を以下の内容で入力する

    ```
    Name: <デフォルトのまま>
    Folder: General
    Prometheus Data Source: Prometheus
    ```

    ![image](https://user-images.githubusercontent.com/63433549/79524419-76d24780-809b-11ea-8352-ae40d5b1cc22.png)

4. インポート完了

    ここからさらにカスタマイズすることもできる
    ![image](https://user-images.githubusercontent.com/63433549/79524843-78503f80-809c-11ea-96cb-b888250c6ca6.png)

# 監視デモ

- ※研修では講師陣が実施するため、受講生は以下の操作を行わない

- 事前に ローカル → 踏み台サーバ → node_exporter を配置したサーバの順番で ssh しておく

    ```
    ssh <spring_board_user_name>@<spring_board_fqdn>
    ssh <node_exporter_user_name>@<node_exporter_private_ip>
    sudo -i
    ```

- 実際にダッシュボードが変化する様子を確認

    -  CPU に負荷をかけてみる

        1. 以下のコマンドを実行する

            ```
            yes > /dev/null
            ```

        2. デモが終わったら `ctrl + c` で抜ける

    - Disk の空き容量を減らしてみる

        1. ダミーファイルを作成する (数値は適宜変更すること)

            ```
            fallocate -l 26GiB dummy_file
            ```

        2. デモが終わったらダミーファイルを削除しておく

            ```
            rm dummy_file
            ```

    - Memory に負荷をかけてみる

        1. 以下のコマンドを実行する (数値は適宜変更すること)

            ```
            /dev/null < $(tail -c 1300M -f /dev/zero)
            ```

        2. デモが終わったら `ctrl + c` で抜けて tail を kill しておく

            ```
            ps -ef | grep tail
            kill <PID>
            ```

    - node_exporter を落としてみる

        1. node_exporter.service を stop する

            ```
            systemctl stop node_exporter.service
            ```

        2. デモが終わったら node_exporter.service を start しておく

            ```
            systemctl start node_exporter.service
            ```

# Appendix

Grafana 公式: [Grafana: The open observability platform \| Grafana Labs](https://grafana.com/)

---

<p style="text-align:center"> <a href="./prometheus_settings"> <- 前へ </a> | <a href="../"> Top </a> | <a href="./alert_setting"> 次へ-> </a> </p>
