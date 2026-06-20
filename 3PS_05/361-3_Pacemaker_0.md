
# Pacemaker

KVM+Vagrantでパッケージ導入したうえでPacemakerを有効化してみる


## Pacemakerのコンポーネント

|コマンド|説明|
|--|--|
|コンポーネント|説明|
|CIB|Cluster Information Base</br>構成管理DB|
|CRMd|Cluster Resource Management Daemon</br>リソース管理の中心|
|PEngine|Policy Engine</br>CIBの情報に基づいて処理を算出|
|LRMd|Local Resource Management Daemon</br>ローカルリソースの監視や操作を行う|
|STONITHd|Shoot The OtherNode In The Head</br>フェンシング(応答しないデバイスの強制停止)|

## ディストリごとの注意事項

- Ubuntu 
  
  特になし  sudo apt でさくっと入る

- CentOS Stream

  特段問題なし、 sudo dnf でさくっと入る

  ```
  # CentOS 8 Streamのとき
  sudo dnf repolist all
  sudo dnf install --enablerepo=ha pacemaker corosync
  ```

- RHEL8

  パッケージが別
  ```
  subscription-manager repos --enable=rhel-8-for-x86_64-highavailability-rpms
  sudo dnf install pcs
  ```

  別途 High Availability Add-on の契約が必要 <-ここ重要


## PacemakerのCRM操作コマンド例

|コマンド|説明|
|--|--|
|crm|引数有の場合、コマンドを実行</br>引数無の場合、crmsh シェルを起動|
|crm_mon|クラスタの現在の動作状態をモニタリング|
|crm_node|クラスタのノード情報を一覧表示|
|crm_simulate|イベントに対するクラスタの応答をシミュレート|
|crm_shadow|設定変更前に変更をサンドボックスで評価|
|crm_verify|設定の構文エラーチェック|
|cibadmin|設定(/var/lib/pacemaker/cib/cib.xml)</br>の検索/追加/削除/更新が行える|

## pcsコマンド
|サブコマンド|説明|
|--|--|
|acl|aclの設定|
|cluster|クラスタ/ノードの設定(インストール例を参照)|
|config|クラスタの全設定の表示管理|
|constraint|リソース制約(後述)の設定|
|property|プロパティ設定|
|resource|クラスタリソースの管理|
|status|クラスタのステータスの表示|
|stonith |フェンシングの設定|


## 実際

- 事前準備(node1/node2両方で実施)
  
  ```
  # パッケージアップデートとhttpd/pcsdプロセスの起動
  sudo yum install -y httpd ; sudo systemctl start httpd 
  sudo yum install -y pacemaker pcs;sudo  systemctl start pcsd
  # firewalldのポート開放:KVMのVagrantでは不要
  sudo firewall-cmd --add-service=high-availability --permanent
  sudo firewall-cmd --reload
  # 各ノードでパスワードを定義
  sudo passwd hacluster
  ```

- pcsを使って構築(以降node1)
  
  ここではIPアドレスで認証してますが、ちゃんと双方の/etc/hosts で名前解決できるようにしましょう..
  ```
  # haclusterのID/PWで相互認証
  sudo pcs cluster auth 10.0.0.11 10.0.0.12
  # /etc/corosync/corosync.conf を自動生成できる
  sudo pcs cluster setup --name ha_cluster 10.0.0.11 10.0.0.12
  # sudo pcs cluster setup --name ha_cluster node1 address=10.0.0.11  node2 address=10.0.0.12
  cat /etc/corosync/corosync.conf
  sudo pcs cluster enable --all
  sudo pcs cluster start --all
  sudo pcs status cluster
  sudo pcs status corosync
  # 各種フラグの設定
  sudo pcs property set stonith-enabled=false
  sudo pcs property set no-quorum-policy=ignore
  sudo pcs property set default-resource-stickiness="INFINITY"
  sudo pcs property list
  # VirtualOPとして10.0.0.20 を設定する
  # curl http://10.0.0.20 をノックするとhttp://10.0.0.11 の結果が返る
  sudo pcs resource create Virtual_IP ocf:heartbeat:IPaddr2 ip=10.0.0.20  cidr_netmask=24 op monitor interval=30s
  sudo pcs status corosync
  # 実際に切ったり揚げたりする
  sudo pcs standby node1
  sudo pcs unstandby node1
  ```



