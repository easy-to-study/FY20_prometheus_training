# IaaSサーバー構築
1. AzurePortalにアクセス  
https://portal.azure.com

2. VirtualMachinesを選択
![image](https://user-images.githubusercontent.com/49776458/79406672-aa956a80-7fd2-11ea-9b45-408161702466.png)

3. 追加を選択
![image](https://user-images.githubusercontent.com/49776458/79406674-abc69780-7fd2-11ea-88bc-928d1a9a84fd.png)

# パラメータの指定
以下の設定値は、確認が必要なパラメータのみ記載しています。  
記載のないパラメータはデフォルト値で問題ありません。

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
<自由に設定>

## 地域
東日本

## イメージ
CentOS-based 7.7

## サイズ(フィルターを解除して選択)
Basic A1
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
![image](https://user-images.githubusercontent.com/49776458/79406677-aec18800-7fd2-11ea-8a63-0a819a191a2d.PNG)
## ディスク

### ディスクのオプション
```
## OSディスクの種類
Standard HDD
```
![image](https://user-images.githubusercontent.com/49776458/79406682-af5a1e80-7fd2-11ea-842c-b443714ff973.png)
## ネットワーク

### ネットワーク インターフェイス
```
## 暗号化の種類
(規定)プラットフォームで管理されるキーを使用した保存時の暗号化

## NIC ネットワーク セキュリティ グループ
なし
```
![image](https://user-images.githubusercontent.com/49776458/79406684-aff2b500-7fd2-11ea-9c86-ff2281228493.png)
## 管理

### 監視
```
## ブート診断
オフ
```
![image](https://user-images.githubusercontent.com/49776458/79406685-b08b4b80-7fd2-11ea-84b2-832a172c8c29.png)
# アクセス
コマンドプロンプトまたは、PowerShellから以下を実施してください。

## 踏み台サーバー
```
## アクセスコマンド
<別途共有>
```

## 皆さんのサーバー
```
## アクセスコマンド
ssh <ユーザー名>@<プライベートIPアドレス>
```