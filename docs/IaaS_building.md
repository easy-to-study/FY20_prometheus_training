# IaaSサーバー構築
1. AzurePortalにアクセス  
https://portal.azure.com

2. VirtualMachinesを選択
![image](https://user-images.githubusercontent.com/49776458/79406672-aa956a80-7fd2-11ea-9b45-408161702466.png)

3. 追加を選択
![image](https://user-images.githubusercontent.com/49776458/79406674-abc69780-7fd2-11ea-88bc-928d1a9a84fd.png)

# パラメータの指定
以下の設定値は、確認が必要なパラメータのみ記載しています。  
記載のない設定値はデフォルト値で問題ありません。

## 基本
### プロジェクトの詳細
```
## サブスクリプション
ITSM_TEST - amaekawa

## リソースグループ
FY20_NEWEMP_TRAIN
```
### インスタンスの詳細
```
## 仮想マシン名
<自由に設定> // 忘れないように！

## 地域
東日本

## イメージ
CentOS-based 7.7

## サイズ
Basic A1 // フィルターを解除して選択
```
### 管理者アカウント
```
## 認証の種類
パスワード

## ユーザー名
<別途共有>

## パスワード
<別途共有>

## パスワードの確認
<別途共有>
```
### 受信ポートの規則
```
## パブリック受信ポート
なし
```
### Smaple
![image](https://user-images.githubusercontent.com/49776458/79406677-aec18800-7fd2-11ea-8a63-0a819a191a2d.PNG)
## ディスク

### ディスクのオプション
```
## OSディスクの種類
Standard HDD

## 暗号化の種類
(既定)プラットフォームで管理されるキーを使用した保存時の暗号化
```
### Smaple
![image](https://user-images.githubusercontent.com/49776458/79406682-af5a1e80-7fd2-11ea-842c-b443714ff973.png)
## ネットワーク

### ネットワーク インターフェイス
```
## 仮想ネットワーク
FY20-VirtualNetwork

##サブネット
Subnet01 (20.10.0.0/24)

## NIC ネットワーク セキュリティ グループ
なし
```
### Smaple
![image](https://user-images.githubusercontent.com/49776458/79406684-aff2b500-7fd2-11ea-9c86-ff2281228493.png)
## 管理

### 監視
```
## ブート診断
オフ
```
### Smaple
![image](https://user-images.githubusercontent.com/49776458/79406685-b08b4b80-7fd2-11ea-84b2-832a172c8c29.png)

**上記の設定値通り入力されたことを確認し、VMを作成する**

# アクセス
コマンドプロンプトまたは、PowerShellから以下を実施してください。

## 踏み台サーバーへ
```
## アクセスコマンド
ssh <別途共有>@<DNS名>
## パスワード
<別途共有>
```

## 皆さんのサーバーへ
```
## アクセスコマンド
ssh <仮想マシン名>@<プライベートIPアドレス>
## パスワード
<別途共有>
```

## アクセス確認
```
## 接続中の仮想マシン名確認
hostname // 本人が作成した <仮想マシン名> が表示されたらアクセス完了！
```
