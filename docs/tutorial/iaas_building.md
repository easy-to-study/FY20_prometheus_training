# IaaSサーバー構築
1. AzurePortalにアクセス
https://portal.azure.com

2. VirtualMachinesを選択
![image](https://user-images.githubusercontent.com/49776559/79426180-b9454700-7ffd-11ea-907e-a1af6bb86413.png)

3. 追加を選択
![image](https://user-images.githubusercontent.com/49776559/79426184-ba767400-7ffd-11ea-80db-c8bd0057fd8b.png)

4. 仮想マシンを選択
![image](https://user-images.githubusercontent.com/49776559/98526757-81fdf680-22bd-11eb-81bc-2c54222e148b.png)

# パラメータの指定
以下の設定値は、確認が必要なパラメータのみ記載しています。

記載のない設定値はデフォルト値で問題ありません。

## 基本
### プロジェクトの詳細
```
## サブスクリプション
ITSM_TEST - amaekawa

## リソースグループ
FY20_NEWEMP_TRAIN_STU01
```
### インスタンスの詳細
```
## 仮想マシン名
<VM_name> // 別途共有

## 地域
東日本

## イメージ
CentOS-based 8.2 - Gen1
// デフォルトで指定されているものを使用してください

## サイズ
Basic_A1
// すべてのサイズを表示をクリックの上、basic と入力し、フィルタリングを行ってください(展開マクロの中にある場合があるので、その場合はクリックして開いてください)
```
### 管理者アカウント
```
## 認証の種類
パスワード

## ユーザー名
<user_name> // 別途共有

## パスワード
<user_password> // 別途共有

## パスワードの確認
<user_password> // 別途共有
```
### 受信ポートの規則
```
## パブリック受信ポート
なし
```
### Sample
![image](https://user-images.githubusercontent.com/24913906/117254790-ce4c7c80-ae83-11eb-9610-4a2c7e8cfb40.png)

## ディスク

### ディスクのオプション
```
## OSディスクの種類
Standard HDD

## 暗号化の種類
(既定)プラットフォームで管理されるキーを使用した保存時の暗号化
```
### Sample
![image](https://user-images.githubusercontent.com/49776458/79406682-af5a1e80-7fd2-11ea-842c-b443714ff973.png)
## ネットワーク

### ネットワーク インターフェイス
```
## 仮想ネットワーク
fy21-virtual-network

##サブネット
subnet01 (20.10.0.0/24)

## パブリック IP
なし
```
### Sample
![image](https://user-images.githubusercontent.com/63433549/116392840-e60c7b00-a85b-11eb-8de0-74fadadc9625.png)
## 管理

### 監視
```
## ブート診断
無効化
```
### 自動シャットダウン
```
## 自動シャットダウンを有効にする
オン

## シャットダウン時刻
18:00:00

## タイムゾーン
(UTC+09:00)大阪、札幌、東京

## シャットダウン前の通知
オフ
```
### Sample
![image](https://user-images.githubusercontent.com/49776559/79420862-b4c86080-7ff4-11ea-9184-8545cb9dc104.png)

**上記の設定値通り入力されたことを確認し、VMを作成する**

# アクセス
コマンドプロンプトまたは、PowerShellから以下を実施してください。

## 踏み台サーバーへ
```
## アクセスコマンド
ssh <spring_board_user_name>@<spring_board_fqdn> -p 443 // 別途共有

// 初回ログイン時にyes/noを聞かれた場合には、yesと入力してください
// パスワードを聞かれたら、別途共有する値を入力してください。なお、入力時の文字列は非表示となります。

## パスワード
<spring_board_user_password> // 別途共有
```

## 皆さんのサーバーへ
```
## アクセスコマンド
ssh <user_name>@<your_private_ip> // 別途共有
## パスワード
<user_password> // 別途共有
```

## アクセス確認
```
## 接続中の仮想マシン名確認
hostname // 本人が作成した <仮想マシン名> が表示されたらアクセス完了！

## ルート権限取得
sudo -i

## パスワード
<user_password> // 別途共有
```

---

<p style="text-align:center"> <a href="../overview/promql"> &lt;- 前へ </a> | <a href="../"> Top </a> | <a href="./service_installation"> 次へ -&gt; </a> </p>
