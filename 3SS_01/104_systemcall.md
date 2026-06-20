---
title:104_システムコールの制御
---

# 104  システムコールの制御

## ライフサイクルを想定したコマンド

### 1. 導入・環境準備 (Installation)

システムコール制御や監視に必要なツール類をインストールします。

- **BCC ツールセットのインストール**:
    - `# dnf install bcc-tools`
    - eBPF を利用して、システムコールを低負荷で監視するためのプログラム群（`execsnoop` や `tcpaccept` など）が `/usr/share/bcc/tools/` 配下に導入されます。
- **bpftrace のインストール**:
    - `# dnf install bpftrace`
    - より柔軟なトレーシングを記述するためのツールです。
- **seccomp-tools の導入（外部ソース）**:
    - `# dnf install seccomp-tools` (または配布元から導入)
    - シラバスで指定されているデバッグ用ツールで、バイナリに適用されている seccomp フィルタのダンプなどに使用します。

### 2. 設定・適用 (Configuration - systemd)

systemd ユニットファイルを使用して、サービスが実行できるシステムコールを制限します。

- **ユニットファイルの編集**:
    - `# systemctl edit <サービス名>`
    - 以下のパラメータを `[Service]` セクションに記述します。
        - **`SystemCallFilter=`**: 許可または拒否するシステムコールのリストを指定します（例：`~@mount` でマウント関連を拒否）。
        - **`SystemCallErrorNumber=`**: フィルタされたシステムコールを呼び出した際に返すエラー番号（例：`EPERM`）を指定します。
        - **`SystemCallArchitectures=`**: 実行を許可する CPU アーキテクチャを制限し、互換レイヤーを介したバイパスを防止します。
- **設定の反映**:
    - `# systemctl daemon-reload`
    - ユニットファイルの変更後、systemd マネージャーに設定を再読み込みさせます。

### 3. 実行・監視 (Monitoring - eBPF)

eBPF を利用して、特定のシステムコールやプロセスの挙動をリアルタイムで監視します。

- **プロセスの実行監視 (`execsnoop`)**:
    - `# /usr/share/bcc/tools/execsnoop`
    - `execve` システムコールを監視し、システム内で新しく起動したプロセスをリアルタイムで表示します。
- **ネットワーク関連システムコールのトレース**:
    - `# /usr/share/bcc/tools/tcpaccept` : `accept()` システムコールを監視し、接続を追跡します。
    - `# /usr/share/bcc/tools/tcpconnect` : `connect()` システムコールを監視し、発信接続を追跡します。
- **ファイル操作の追跡 (`filelife`)**:
    - `# /usr/share/bcc/tools/filelife`
    - `stat()` システムコールを監視し、短寿命のファイルを検出します。

### 4. トラブルシューティング・デバッグ (Troubleshooting)

seccomp フィルタの動作確認や、拒否されたシステムコールの特定を行います。

- ** seccomp フィルタのダンプ (`seccomp-tools`)**:
    - `$ seccomp-tools dump <実行ファイル>`
    - プログラムに設定されている BPF フィルタの内容を人間が読める形式で表示し、解析します。
- **サービスのステータス確認**:
    - `$ systemctl status <サービス名>`
    - システムコール制限によってサービスが異常終了していないか、エラーログとともに確認します。
- **詳細ログの確認**:
    - `# journalctl -u <サービス名> -e`
    - システムコールエラーによる拒否が記録されていないか確認します。
- **監査ログによる拒否の特定**:
    - `# ausearch -m SECCOMP -ts recent`
    - Audit システムが有効な場合、seccomp によって拒否されたイベントを検索できます（シラバス 2.4 監査との関連）。
