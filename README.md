# CI/CDのサンプル
## 目的
- GitHub Actionsを用いた、PythonのCI/CD環境を構築すること

## 実行環境

- Mac OS Ventura 13.4
- Visual Studio Code 1.88.1

## 用意する環境
- サンプルアプリ
    - [FastAPI](https://fastapi.tiangolo.com/ja/)
- パッケージ管理
    - [Rye](https://rye-up.com/)
        - pipも利用できますが、[uv](https://github.com/astral-sh/uv)を使います
- linter & formatter
    - [Ruff](https://docs.astral.sh/ruff/)
- Unit Test
    - pytest
- Integration Test
    - locast(検討中)

## 準備
- 自分の作業ログとして残します。最新のインストール方法等は公式を参考にしてください。

### インストール

- 主にryeのインストール手順を残す。他のライブラリはrye経由で追加
- パッケージ管理ツールの選択時に、`uv`を選択
```bash
$ curl -sSf https://rye-up.com/get | bash
$ echo 'source "$HOME/.rye/env"' >> ~/.bashrc
$ source ~/.bashrc
$ rye init (YOUR_WORKFOLDER_PATH)
$ rye pin (PYTHON_VERSION)
$ rye sync
$ rye add (LIBRARY)
```

### 実行確認
- FastAPIの実行確認用のスクリプトを作る



