
# Keepalivedの構成サンプル-1

## Keepalivedの最小ネットワーク構成

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

## 作業手順

- 予めCentOS7 でminimal構成を構築し、それぞれnode1..node3とする
- node2、node3 にそれぞれhttpd を構築する
  ```
  yum install -y httpd ; systemctl enable httpd
  ```
- node1にてipvsadm と keepalived をインストールする
  ```
  yum install ipvsadm keepalived
  echo 'net.ipv4.ip_forward = 1' >> /etc/sysctl.conf
  systemctl enable ipvsadm keepalived
  ```
- Node1のコンソールで、以下の順にコマンドを実行する
  ```
  touch /etc/sysconfig/ipvsadm 
  systemctl start ipvsadm 
  ipvsadm -C ; ipvsadm –S   # テーブル初期化
  ipvsadm -A -t 192.168.1.12:80 -s wlc
  ipvsadm -a -t 192.168.1.12:80 -r 192.168.2.13:80 –m
  ipvsadm -a -t 192.168.1.12:80 -r 192.168.2.14:80 –m
  ipvsadm -S ; ipvsadm -ln # テーブルの確認
  ```
- curl http://192.168.1.12 の実行時に、Node2/Node3が表示される


