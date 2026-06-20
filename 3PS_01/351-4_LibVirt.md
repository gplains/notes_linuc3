# Libvirt



Linux上の仮想化の管理操作における共通インタフェース

もともとはXenに対応するAPIとして開発されたもの

対応基盤は多岐にわたる
- KVM/QEMU Xen 
- LXC OpenVZ Virtuozzo
- Virtualbox 

libvirtdの基本アーキテクチャ

- 中心: libvirtd (デーモン)
- 設定: /etc/libvirtd/libvirtd.conf

### libvirt の管理コマンド-1

- 管理ユーティリティ virsh <サブコマンド> <引数>

  https://libvirt.org/manpages/virsh.html

- virsh のサブコマンド例

|サブコマンド|説明|
|--|--|
|create|XMLファイルからゲストVMを作成|
|start|ゲストのVMを起動|
|shutdown/destroy|ゲストのVMを停止/強制停止|
|console|ゲストのVMに接続|
|dumpxml|ゲストVMの設定ファイルをXML形式で出力|
|dump|ゲストVMのコアダンプを出力|
|net-edit|ゲストVMのネットワーク設定をXML形式で編集|
|migrate [--live]|ゲストVMを別ホストに移行</br>--live:ライブマイグレーション|

- 管理ユーティリティ virt-***

  virshでは煩雑になる操作をまとめたユーティリティ群

- virt-**コマンドの例

|コマンド|説明|
|--|--|
|virt-install |メディアから仮想マシンを作成する|
|virt-image|XMLファイルから仮想マシンを作成する|
|virt-clone|仮想マシンを複製する|
|virt-viewer|仮想マシンのGUIコンソールを表示</br>※要X環境|
|virt-manager|仮想マシンのGUI管理ツール</br>※要X環境|

### libvirtのネットワーク管理

- 規定でネットワークdefault(virbr0)が作成される

  物理NIC - 仮想ブリッジ(virbr0) – TAP(vnet0) – 仮想NIC(eth0)
  
  virbr0 、 vnet0 はlibvirtd デーモンが管理する
  
  ホストOS:virbr0 、ゲスト側: 192.168.122.0/24(NAT)

- コマンドの利用例
  - アクティブな仮想ネットワークの表示
  
    virsh net-list
  - デフォルトネットワークの起動
  
    virsh net-start default
  - デフォルトネットワークの自動起動の設定
  
    virsh net-autostart default
  - ネットワーク設定の編集(XML形式)
  
    virsh net-edit 

- 別途NetworkManager経由(CentOS7/8の場合)でブリッジを作成することも可能

### libvirt:KVMでの仮想マシンのライフサイクル(例)

KVMでの仮想マシン起動停止は、virsh(libvirt)に依存する
- 仮想マシンの作成
  
  virt-install --name domain1 … #domain1 が生成される
- 仮想マシンの起動

  virsh start domain1 [--console]
  
  ※ --console 付与時はコンソール接続
- 仮想マシンへ接続 (抜けるときは、 Ctrl + ] )
  
  virsh console domain1 
- 仮想マシンの停止
  
  virsh shutdown domain1
  
  ※ shutdown:シャットダウン、destroy:強制停止
- 仮想マシンの削除
  
  virsh undefine domain1
  
  ※稼動中の場合、停止後に削除される


## 知識分野
- 出題数:9
- libvirtの構造を理解する。
- libvirtコネクションとノードを管理する。
- スナップショットを含む、QEMUとXenドメインの作成と管理。
- ドメインのリソース消費の管理と解析。
- ストレージプールとボリュームの作成と管理。
- 仮想ネットワークの作成と管理。
- ノード間のドメインのマイグレーション。
- libvirtがXen,QEMUをどのように操作するか理解する。
- libvirtがdnsmasqやradvdなどのネットワークサービスをどのように操作するか理解する。
- libvirt XML設定ファイルを理解する。
- virtlogdとvirtlockdの知識。

## 用語

- libvirtd
- /etc/libvirt/
- virsh (関連するサブコマンドを含む)
