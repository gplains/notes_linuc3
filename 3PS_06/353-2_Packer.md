### Packerのインストール

公式サイトに従う

https://developer.hashicorp.com/packer/install

- RHEL系
  ```
  sudo yum install -y yum-utils
  sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
  sudo yum -y install packer
  ```
- Ubuntu系
  ```
  wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
  echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
  sudo apt update && sudo apt install packer
  ```


  ## Packer サブコマンド
|サブコマンド|説明|
|--|--|
|build|ビルドの実行|
|console|対話型プロンプト|
|fix|テンプレートの修正|
|fmt|HCL2テンプレートを書式変換|
|init|テンプレートに沿ってプラグインをダウンロード|
|plugins|Packerプラグインのインストール/アンインストール|
|validate|テンプレートの構文チェック|
|inspect|テンプレートを解析してコンポーネント情報を出力|
|version|Packer自身のバージョン表示|

### 用語
- Packer

