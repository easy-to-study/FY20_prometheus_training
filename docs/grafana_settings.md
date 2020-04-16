# Grafana Web UI にログイン

1. ブラウザから以下のURLにアクセスする

    ```
    http://<jump_domain>/<your_private_ip>/grafana/
    ```

2. デフォルトでは admin / admin でログインできる

    ![image](https://user-images.githubusercontent.com/63433549/79427916-628d3c80-8000-11ea-86fc-c04b3ddcdca1.png)

3. パスワード変更画面へ遷移するが、今回は Skip する

    ![image](https://user-images.githubusercontent.com/63433549/79428463-25757a00-8001-11ea-824a-57f635c21d90.png)

4. ログインできたら以下の画面へ遷移する

    ![image](https://user-images.githubusercontent.com/63433549/79428547-4342df00-8001-11ea-8169-172679cac015.png)

# Data Source を追加する

1. サイドメニューから歯車アイコン → Data Sources を押下する

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

    - 今回は Prometheus と Grafana を同じサーバに配置しているため、localhost:9090 を指定している

    - Prometheus のデフォルト設定では Port: 9090 となっている (詳細は Appendix を参照)

    ![image](https://user-images.githubusercontent.com/63433549/79429978-345d2c00-8003-11ea-88d4-bc9ae65394c5.png)

5. Save & Test を押下してエラーが表示されなければ成功

# Grafana から PromQL を実行してみる

1. サイドメニューから方位磁石みたいなアイコン → Explore を押下する

    ![image](https://user-images.githubusercontent.com/63433549/79431077-e47f6480-8004-11ea-8432-9c0700a5b427.png)

2. 任意の PromQL を入力して Run Query を押下

    Prometheus の Graph 同様にクエリ結果を確認することができた
    ![image](https://user-images.githubusercontent.com/63433549/79431528-88691000-8005-11ea-97e6-89613189efe9.png)
