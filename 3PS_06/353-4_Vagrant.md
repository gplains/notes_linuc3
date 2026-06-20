### Vagrantのインストール

公式サイトに従う

https://developer.hashicorp.com/packer/install

- RHEL系
  ```
  sudo yum install -y yum-utils
  sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
  sudo yum -y install vagrant
  ```
- Ubuntu系
  ```
  wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
  echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
  sudo apt update && sudo apt install vagrant
  ```

## Vagrant サブコマンド
|サブコマンド|説明|
|--|--|
|init|Vagrantfileを作成|
|up|仮想マシンの作成/起動|
|ssh|仮想マシンにSSH接続|
|halt|仮想マシンの停止|
|status|仮想マシンの状態表示|
|destroy|仮想マシンの破棄|

## 用語
- vagrant
- Vagrantfile