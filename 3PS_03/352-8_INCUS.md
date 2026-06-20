
### LXCとLXD/INCUSの関係

- LXCはコンテナのレイヤ
- LXDはLXCを操作するフロントエンド的なもの

  LXDからフォークされたものがINCUS


### incus のインストール

https://incus-ja.readthedocs.io/ja/latest/installing/

- RHEL/Alma
  
  公式なインストール方法はなさそう

- Rocky
  ```
  dnf -y install epel-release
  dnf copr enable neil/incus
  dnf config-manager --enable crb
  dnf install incus incus-tools
  ```

- Ubuntu

  ```
  sudo apt install incus
  apt install qemu-system # Option
  apt install incus-tools # LXDから移行するオプションコマンド:lxd-to-incus
  ```


### incusの管理コマンド

基本は incus <サブコマンド> [引数]

歴史的経緯からLXDとIncusはほぼ同じ

|サブコマンド|説明|
|--|--|
|create|コンテナ作成|
|init|コンテナ作成|
|launch|コンテナ作成して実行|
|start/stop|コンテナ起動/停止|
|delete/rm|コンテナ削除|
|exec|コンテナでコマンドを実行</br> exec <container> -- bash でシェル起動、の替り|
|console|コンテナにログイン(事前にVMでアカウントを作ること)|
|list(ls)|コンテナの情報表示|
|info|コンテナの詳細情報表示|
|copy(cp)|コンテナの複製|
|rename/move|コンテナの移動、リネーム|
|image|イメージ操作(list,copy,info,refresh,delete...)|
|profile|プロファイル操作|
|network|ネットワーク|
|storage|ストレージ|
|snapshot|スナップショット操作|
|config|ホスト側設定|
|publish|イメージの作成(container/snapshotから)|

### incus の一般的な流れ

```
# 初期化
incus admin init --minimal

# ubuntu/noble でcontainerを作成
sudo incus launch images:ubuntu/noble noble2
sudo incus list

# lxc-attach が使えないので、対象コンテナでbashを実行
sudo incus exec noble2 -- bash

# 停止して削除
 sudo incus stop noble2
 sudo incus delete noble2 ; sudo incus list 
```

### 用語
- lxc
- lxd