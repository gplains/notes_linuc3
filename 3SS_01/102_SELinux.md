---
title:102_SELinux による強制アクセス制御
---

# 102 SELinux による強制アクセス制御

## SELinux

- RHEL7/8 および、これらのクローンでの実施を推奨
  
  (Mastering Linux Security and Hardening)
- Ubuntu20.04 では少し具合が悪い(仮想マシン上でインストール再起動すると高確率でフリーズする)


## Ubuntu 20.04 の場合

※Ubuntuの場合、唐突にenforcing にすると詰むのでpermissive で色々試しましょう

- インストール

  ```
  # apparmor を完全削除 、して再起動
  sudo systemctl stop apparmor
  sudo apt remove apparmor -y
  sudo reboot
  # auditdをインストール
  sudo apt install auditd  # audit2why他でお世話になるので
  # selinux一式をインストール
  sudo apt install selinux-basics selinux-policy-default selinux-utils setools selinux-policy-src
  sudo selinux-activate
  sudo reboot

  # 状態を確認
  sestatus  # permissive が返る...はず
  sudo ls -l /var/log/audit # audit.log が出力されている

  # ポリシ不一致で大量にエラーが出るのでとりあえず黙らせる
  sudo audit2allow -M somepolicy < /var/log/audit/audit.log
  sudo semodule -i somepolicy.pp  # ポリシ反映
  sudo reboot
  ```



## CentOS 8.x の場合

- インストール

   標準でインストール済み



## 各種コマンド

- 状態確認

  ```
  # 有効無効の確認
  getenforce
  # 詳細な状態の確認
  sestatus
  # selinux の統計を出力
  seinfo
  ```


- モード変更
  ```
  # 一時的な変更
  sudo setenforce [Permissive|enforcing]
  # 恒久的な変更
  sudo sed -ie "s/^\(SELINUX=\)enforcing/\1permissive/" /etc/selinux/config
  sudo reboot
  ```

- SELinuxに関する各種操作

  ```
  # SELinuxに関する管理全体を司る
  semanage
  ```

- コンテキスト
  
  ```
  # セキュリティコンテキストの変更
  chcon
  # ファイルのセキュリティコンテキストを復元
  restorecon
  # 指定したセキュリティコンテキストでコマンドを実行
  runcon

  # SELinuxの設定ファイルに従ってセキュリティコンテキストを変更
  fixfiles
  # 指定ファイルに従ってセキュリティコンテキストを変更
  setfiles
  
  # ブール値を表示 
  getsebool -a # すべて表示
  getsebool [指定パラメタ] # 指定のパラメタに対するブール値を表示
  # ブール値を変更
  setsebool [指定パラメタ] {on|off} 
  togglesebool # ON|OFFの切り替え
  ```

- ポリシ解析等

  ```
  # 要setools-gui
  apol 
  # RHEL8にはない？SELINUXのログを監査
  seaudit
  # RHEL8にはない？SELINUXのログを監査しレポート出力
  seaudit-report
  # 監査ログから原因を出力
  audit2why
  # 監査ログから対策を表示
  audit2allow
  ```

## ライフサイクルを想定したコマンド

### 1. 現状確認と調査 (Investigation)

システムの SELinux 状態やポリシーの詳細を確認するためのコマンドです。

- **`getenforce`**: 現在の動作モード（Enforcing, Permissive, Disabled）を表示します。
- **`sestatus`**: SELinux の現在の詳細なステータス、ロードされているポリシー名（targeted, mls 等）を表示します。
- **`seinfo`**: インストールされているポリシーの統計（ユーザー、ロール、タイプ数など）を表示します。
- **`sesearch`**: ポリシー内の具体的なアクセス許可ルール（allow ルール等）を検索します。

### 2. 動作モードの管理 (Mode Management)

動作モードの一時的または永続的な変更を行います。

- **`setenforce [1|0]`**: 動作モードを一時的に変更します（1: Enforcing, 0: Permissive）。
- **`/etc/selinux/config` の編集**: `SELINUX=` 行を書き換えることで、次回起動時からのモードを永続的に設定します。
- **`grubby`**: ブート時のカーネル引数を変更して SELinux を無効化（`selinux=0`）したり、モードを指定したりするために使用します。
- **`semanage permissive -a <タイプ>`**: システム全体は Enforcing のまま、特定のドメイン（タイプ）のみを Permissive モードに設定します。

### 3. コンテキスト（ラベル）の管理 (Context Management)

ファイルやプロセスに割り当てられた SELinux コンテキストの表示・変更・修正を行います。

- **コンテキストの表示 (`-Z` オプション)**:
    - **`ls -Z`**: ファイルやディレクトリのコンテキストを表示します。
    - **`ps -Z`**: プロセスのコンテキストを表示します。
- **コンテキストの変更と適用**:
    - **`chcon`**: ファイルのコンテキストを一時的に（手動で）変更します。
    - **`semanage fcontext`**: ポリシー上のデフォルトのファイルコンテキスト定義を追加・変更します。
    - **`restorecon`**: ポリシーの定義に基づき、ファイルのコンテキストを正しい（デフォルトの）状態に復元します。
    - **`matchpathcon`**: 特定のパスに対してポリシーが期待するコンテキストを表示し、現状と比較します。
- **再ラベル付け**:
    - **`fixfiles -F onboot`**: 次回起動時にシステム全体のファイルコンテキストを強制的に再スキャン・修正するように設定します。

### 4. ブール値によるポリシー調整 (Boolean Management)

ポリシー全体を再構築することなく、オン/オフのスイッチ（ブール値）で機能を調整します。

- **`getsebool -a`**: 利用可能なすべてのブール値とその現在の状態を表示します。
- **`setsebool -P <ブール名> [on|off]`**: ブール値を変更します。`-P` オプションを付けることで、再起動後も変更を維持（永続化）させます。

### 5. 監視・トラブルシューティング・カスタムポリシー (Troubleshooting & Custom Policy)

拒否ログの特定と、必要に応じたカスタムルールの作成を行います。

- **ログの分析 (`ausearch`, `sealert`)**:
    - **`ausearch -m AVC -ts recent`**: 最近発生した SELinux の拒否メッセージ（AVC）を検索します。
    - **`sealert -l "*"`**: ログを解析し、人間が読める形式で原因と解決策の提案を表示します。
- **拒否のデバッグ**:
    - **`semodule -DB`**: ポリシーで非表示にされている拒否ログ（dontaudit）を一時的に表示するように設定します。`semodule -B` で元に戻します。
- **カスタムポリシーの作成**:
    - **`audit2allow`**: 拒否ログからそのアクセスを許可するためのポリシーモジュールを自動生成します。
    - **`sepolicy generate --init <バイナリ>`**: 特定の実行ファイル用に新しい SELinux ポリシーのテンプレートを生成します。
- **モジュールの管理**:
    - **`semodule -i <モジュール名>.pp`**: カスタムポリシーモジュールをインストールします。
    - **`semodule -l`**: インストールされているポリシーモジュールを一覧表示します。
