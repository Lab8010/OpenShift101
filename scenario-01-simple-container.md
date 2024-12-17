# サンプルコマンド紹介

このページでは、1つのコンテナを実行し、動作確認などに使用するコマンドを紹介します。

---

## コマンドと出力のまとめ

```bash
# プロジェクト名 ocp-learning を作成する
oc new-project ocp-learning

# Pod名 my1stpod として、Red Hatが提供する httpd-24 イメージを使用したコンテナを起動する
oc run my1stpod --image registry.access.redhat.com/ubi9/httpd-24

# Podの起動している様子を確認する
oc get pods

# 出力例:
# NAME       READY   STATUS    RESTARTS   AGE
# my1stpod   1/1     Running   0          2m12s

# Podの起動している様子をより詳細に確認する
oc get pods -o wide

# 出力例:
# NAME       READY   STATUS    RESTARTS   AGE     IP            NODE   NOMINATED NODE   READINESS GATES
# my1stpod   1/1     Running   0          2m48s   10.217.0.66   crc    <none>           <none>

# Pod名 my1stpod に関する詳細情報を取得する
oc describe pod/my1stpod

# Pod名 my1stpod のログを表示する
oc logs pod/my1stpod
