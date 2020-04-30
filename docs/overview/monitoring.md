# monitoring(監視)

## 監視システムの種類

大きく「Push型アプローチ」と「Pull型アプローチ」と分けられます。
![image](https://user-images.githubusercontent.com/24913906/79302727-1ff83100-7f28-11ea-8571-06493ad4b7da.png)

### Pull型アプローチ

Pull 型アプローチでは、監視サーバ上に監視対象についての設定を行い、監視対象からデータを集めてくる形のアプローチがとられています。

古くからある監視システムはこちらのアプローチをとっていることが多いように思います。Pull 型の監視システムとしては以下のようなものがあります。

- [Zabbix](https://www.zabbix.com/jp)
- [Prometheus](https://prometheus.io/)

### Push型アプローチ

Push 型のアプローチでは、監視対象となるホストにエージェントをインストールし、各ホストのエージェントが監視サーバに対してデータを送信する形で実現がなされています。

管理サーバ側で設定変更を行わずとも、新しい監視対象が増えた場合には、対象のサーバにエージェントを追加するだけで監視対象を増やすことができます。Push 型の監視システムとして以下のようなものがあります。

- [Sensu](https://sensu.io/)
- [Datadog](https://www.datadoghq.com/ja/?utm_source=Advertisement&utm_medium=GoogleAdsNon1stTierBrand&utm_campaign=GoogleAdsNon1stTierBrand-JPNLangEN&utm_content=Brand&utm_keyword=%2Bdatadog&utm_matchtype=b&gclid=CjwKCAjwvtX0BRAFEiwAGWJyZOwjPjcc4rAofuU8c6ND9RFInP8XGbcaADPhdfuSkeGSv68Do0BrZhoCEU8QAvD_BwE)

## 監視の分類

監視システムを導入する上で大切な観点が２つあります。

### proactive(積極的)監視

- 問題がないことを確認する監視
- 問題が起こった場合に気づけるようにしておくとよい


### reactive(反応的)監視

- 問題を契機とした調査
- 問題が起きたときの情報、現在の情報などを見れるようにしておくとよい

## まとめ

監視ツールによって、特色（メリット・デメリット）や費用の関係もあるので使用目的にあったものをチョイスして利用することが重要になります。
![image](https://user-images.githubusercontent.com/24913906/79536677-95e0d180-80bb-11ea-82e2-aaa6f44f4a15.png)

Prometheusでは、pull型のデメリットの部分（監視を行うサーバが増えた際に監視サーバの設定変更が必要）をService Discoveryという、サーバを自動で検知してくれる仕組みが補ってくれるため、Pull型のデメリットである部分を払拭しているといえます。

また、必要十分な情報だけを収集するのではなく、多くのメトリクスを収集し必要な情報をクエリを用いて取り出す設計になっているため、監視の分類で記載した２種類についても実現が容易となっています。
