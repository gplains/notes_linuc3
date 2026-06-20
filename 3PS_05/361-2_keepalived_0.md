# LVS(ipvsadm)とKeepalived


## 作業手順

- ipvsadm と keepalived をインストールする
  ```
  yum install ipvsadm keepalived
  echo 'net.ipv4.ip_forward = 1' >> /etc/sysctl.conf
  systemctl enable ipvsadm keepalived
  ```
## ipvsadm のサブコマンド

|サブコマンド|説明|
|---|---|
|A --add-service|仮想サーバの追加|
|a --add-server|仮想サーバのリアルサーバ(下位サーバ)定義追加|
|D --delete-service|仮想サーバの削除(下位サーバごと削除)|
|d --delete-server|仮想サーバのリアルサーバ定義削除|
|E --edit-service|仮想サーバの編集|
|e --edit-server|仮想サーバのリアルサーバ定義編集|
|--start-daemon|接続同期デーモンを起動|
|--stop-daemon|接続同期デーモンを停止|

## keepalivedのスケジューリングアルゴリズム

|記号|説明|
|---|---|
|rr / wrr|ラウンドロビン<br/>(wrrは重みつきラウンドロビン)|
|lc/wlc|最小コネクション<br/>(wlcは重みつき最小コネクション)<br/>※未指定の場合、wlc が選択される|
|lblc|ローカリティベース<br/>最小コネクション|
|lblcr|ローカリティベースレプリケーションつき<br/>最小コネクション|
|dh|宛先ハッシュ(宛先IPアドレス基準)|
|sh|送信元ハッシュ(送信元IPアドレス基準)|
|sed|最小遅延予測|
|nq|キューなしを優先、埋まっていれば最小遅延予測|

