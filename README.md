# codex_base_image

Codex をチーム環境で使うための devcontainer 向けベースイメージです。

このイメージでは以下を前提にしています。

- `@openai/codex` をグローバルインストール
- `bubblewrap` をインストールし `/usr/bin/bwrap` を setuid 化
- `/etc/codex/requirements.toml` で管理者ルールを強制
- `.env` や代表的な秘密情報ファイルの読み取りを禁止
- `danger-full-access` は許可しない
- devcontainer 側で sandbox 用 capability と security option を付与

## Files

- `Dockerfile`: ベースイメージ定義
- `requirements.toml`: Codex の管理者強制設定
- `.devcontainer/devcontainer.json`: devcontainer 利用例

## Build

```bash
docker build -t codex-base-image /home/keita/projects/codex_base_image
```

## Devcontainer

`.devcontainer/devcontainer.json` には、Codex の内側 Linux sandbox を Docker 内で成立させるための `runArgs` を入れています。

- `--cap-add=SYS_ADMIN`
- `--cap-add=SYS_CHROOT`
- `--cap-add=SETUID`
- `--cap-add=SETGID`
- `--security-opt=seccomp=unconfined`
- `--security-opt=apparmor=unconfined`

## Verify

コンテナ起動後、`/etc/codex/requirements.toml` と `bwrap` を確認します。

```bash
ls /etc/codex
which bwrap
ls -l /usr/bin/bwrap
codex --help
```

必要に応じて `requirements.toml` の `deny_read` に組織固有の秘密ファイルパターンを追加してください。
