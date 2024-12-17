# サンプルコマンド: OCP外と通信できるコンテナの作成

このページでは、1つのコンテナを実行し、**OCP外と通信できるようにするコマンド**を紹介します。  
※プロジェクトおよびコンテナを0から作成することを想定したコマンドです。

---

## コマンドと出力のまとめ(上から順に実行していく想定)

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

# 上記で作成された Pod名 my1stpod に対するサービスを作成する
# (使用しているコンテナはポート番号8080でWebページを公開しているイメージ)
oc expose pod/my1stpod --port 8080

# 作成されたサービスを確認する
oc get service

# 出力例:
# NAME       TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
# my1stpod   ClusterIP   10.217.5.111   <none>        8080/TCP   33s

# 作成されたサービス名 my1stpod に対するルートを作成する
oc expose service/my1stpod

# 出力例:
# route.route.openshift.io/my1stpod exposed

# 作成されたルートを確認する
oc get route

# 出力例:
# NAME       HOST/PORT                                PATH   SERVICES   PORT   TERMINATION   WILDCARD
# my1stpod   my1stpod-ocp-learning.apps-crc.testing          my1stpod   8080                 None

# 公開されたPod (my1stpod) にWebアクセスができるかを確認する
# (firefoxなどのブラウザでアクセスしても良い)
curl my1stpod-ocp-learning.apps-crc.testing

# 出力例: (以下はコンテナイメージ内のWebページ)
# <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">
# <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
#   <head>
#     <title>Test Page for the HTTP Server on Red Hat Enterprise Linux</title>
#     <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
#     <style type="text/css">
#       /*<![CDATA[*/
