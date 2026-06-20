
# HAPROXY

## 作業手順

- コンソールで、以下の順にコマンドを実行する
  ```
  yum -y install haproxy
  systemctl enable haproxy
  vi /etc/haproxy/haproxy.cfg　# 詳細な設定箇所については省略
  systemctl start haproxy
  ```

## haproxy の振り分けアルゴリズム

|記号|説明|
|---|---|
|roundrobin|重み付けが動的に設定可能なラウンドロビン|
|static-rr|重み付けが静的に設定可能なラウンドロビン|
|leastcon|最小接続|
|source|送信元IPアドレスのハッシュ|
|uri|リクエストURI(の?より前)をハッシュ|
|url_param|GETリクエストにおけるクエリ文字列|
|hdr(名前)|HTTPヘッダで振り分け|
|rdp_cookie(名前)|RDPクッキーで振り分け|

## HAPROXYの最小ネットワーク構成

```
                  ┌──────────────────────────────────┐
                  │          192.168.2.0/24          │
                  │      ┌─────────┬──────────┐      │
                  │      │ 12      │ 13       │ 14   │
    ┌─────────┐   │ ┌────┴───┐ ┌───┴────┐ ┌───┴────┐ │
    │         │   │ │  Node1 │ │ Node2  │ │ Node3  │ │
    │ Console │   │ │   LB   │ │ Server1│ │ Server2│ │
    └────┬────┘   │ └────┬───┘ └────────┘ └────────┘ │
         │ 11     │   12 │                           │
         └────────┼──────┘                           │
 192.168.1.0/24   │          仮想化基盤               │
                  └──────────────────────────────────┘
```

## サンプルの作業手順

- 予めCentOS7 でminimal構成を構築し、それぞれnode1..node3とする
- Node1のコンソールで、以下の順にコマンドを実行する
  ```
  yum -y install haproxy
  systemctl enable haproxy
  vi /etc/haproxy/haproxy.cfg　# 詳細な設定箇所については省略
  systemctl start haproxy
  ```
- curl http://192.168.1.12 の実行時に、Node2/Node3が表示される

