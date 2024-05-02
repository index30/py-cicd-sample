# CI/CDのサンプル
## 目的
- GitHub Actionsを用いた、PythonのCI/CD環境を構築すること

## 実行環境

- Mac OS Ventura 13.4
- Visual Studio Code 1.88.1

## 用意する環境
- サンプルアプリ
    - [FastAPI](https://fastapi.tiangolo.com/ja/)
    - uvicornのインストールも併せて行う
- パッケージ管理
    - [Rye](https://rye-up.com/)
        - pipも利用できますが、[uv](https://github.com/astral-sh/uv)を使います
- linter & formatter
    - [Ruff](https://docs.astral.sh/ruff/)
- Unit Test
    - [pytest](https://docs.pytest.org/en/8.2.x/)
- Integration Test
    - [locast](https://docs.locust.io/en/stable/index.html)

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
    - [公式](https://fastapi.tiangolo.com/ja/#_4)に記載されているコードを流用
- formatter, linterの実行
    ```bash
    $ rye run ruff format --check # 配下のファイルのフォーマットチェック
    $ rye run ruff format . # (エラーが起きた場合)フォーマット実行
    $ rye run ruff check . --fix # リンターチェックと、エラー時の修正
    ```
- GitHub Actionsの設定
    - トリガーを設定し、Marketplaceにあるrye・ruff用のワークフローを挿入する
    ```yaml
    steps:
      - uses: actions/checkout@v4
      # ryeのセットアップ
      - uses: sksat/setup-rye@v0.22.0
      # ruffのセットアップ
      - uses: chartboost/ruff-action@v1
        with:
          args: 'format --check'
      - name: Sync Rye
        run: rye sync
    ```
    - pytestの実行
    ```yaml
      - name: Run Pytest
        run: rye run python -m pytest
    ```
    - locustの実行
      - uvicornを使った検証としたため、nohupによるバックグラウンド実行を行い、シャットダウンする形に
    ```yaml
      - name: Run uvicorn
        run: |- 
          cd src
          nohup rye run uvicorn main:app --host=0.0.0.0 --port=8000 > uvicorn.log 2>&1 & echo $! > ../uvicorn_pid.txt
      - name: Run locust
        run: rye run locust --host=http://0.0.0.0:8000 --headless -u 1 --run-time 10
      - name: Shutdown uvicorn
        run: kill -9 `cat uvicorn_pid.txt`
    ```
