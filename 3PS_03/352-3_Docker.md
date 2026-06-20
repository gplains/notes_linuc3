## Docker
- 出題数:9
- Dockerのアーキテクチャとコンポーネントを理解している。
- Dockerレジストリからイメージを利用して、Dockerコンテナを管理することができる。
- Dockerコンテナのイメージとボリュームを理解し管理する
- Dockerコンテナのログ取得を理解し管理する。
- Dockerのネットワーク機能を理解し管理する。
- コンテナイメージを作成するために、Dockerfileを利用することができる。
- レジストリDockerイメージを利用して、Dockerレジストリを実行する。

### Docker/Podman のインストール


- RHEL/Alma/Rocky
  
  ```
  sudo yum install -y yum-utils
  sudo yum-config-manager --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
  sudo yum install docker-ce docker-ce-cli containerd.io
  sudo systemctl start docker
  ```
    dnf install podman # podman

- Ubuntu

  apt install docker  #docker
  apt install podman podman-docker # podman

### Dockerの管理コマンド
サブコマンド|説明|
|--|--|
|build|イメージ作成|
|start/stop|コンテナ起動/停止|
|run|コンテナ作成＋実行|
|attach|起動中のコンテナに接続|
|rm|コンテナを削除|
|rmi|コンテナイメージを削除|
|pull/push|DockerHubからイメージを受信/送信|

### 用語
- dockerd
- /etc/docker/daemon.json
- /var/lib/docker/
- docker
- Dockerfile
