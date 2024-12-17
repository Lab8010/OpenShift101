# サンプルコマンド: Deploymentリソースの作成と通信確認

このページでは、Red Hatが提供するhttpdイメージを使用し、**Deploymentリソースを展開後、動作確認**などに使用するコマンドを紹介します。

※プロジェクトおよびコンテナを0から作成することを想定したコマンドです。

---

## コマンドと出力のまとめ(上から順に実行していく想定)

```bash
# プロジェクト名 ocp-learning を作成する
oc new-project ocp-learning

# Deployment名 my1stdeploy として、Red Hatが提供する httpd-24 イメージを使用したデプロイメントとして展開されるPodを起動する
oc create deployment my1stdeploy --image registry.access.redhat.com/ubi9/httpd-24

# 出力例:
# deployment.apps/my1stdeploy created

# Podの起動している様子を確認する
oc get pods

# 出力例:
# NAME                           READY   STATUS    RESTARTS   AGE
# my1stdeploy-6cb9b94896-r5zdb   1/1     Running   0          5m27s

# サービス、DeploymentConfig、BuildConfig などの現在のプロジェクトについての情報を表示する
oc status

# 出力例:
# In project ocp-learning on server https://api.crc.testing:6443
# http://my1stpod-ocp-learning.apps-crc.testing to pod port 8080 (svc/my1stpod)
#   pod/my1stpod runs registry.access.redhat.com/ubi9/httpd-24
# deployment/my1stdeploy deploys registry.access.redhat.com/ubi9/httpd-24
#   deployment #1 running for 4 minutes - 1 pod

# 上記で作成された Deployment 名 my1stdeploy に対するサービスを作成する
# (使用しているコンテナはポート番号8080を使用してWebページを提供し、フロントエンド側のポート番号80に80に関連付けWebページを公開しているイメージ)
oc expose deployment/my1stdeploy --name svc-my1stdeploy --port 80 --target-port 8080

# 出力例:
# service/svc-my1stdeploy exposed

# 作成されたサービスを確認する
oc get services

# 出力例:
# NAME              TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
# svc-my1stdeploy   ClusterIP   10.217.5.160   <none>        80/TCP    5m6s

# 上記で作成されたサービス名 svc-my1stdeploy に対するルートを作成する
oc expose service/svc-my1stdeploy --name route-my1stdeploy

# 出力例:
# route.route.openshift.io/route-my1stdeploy exposed

# 作成されたルートを確認する
oc get route

# 出力例:
# NAME                HOST/PORT                                         PATH   SERVICES          PORT   TERMINATION   WILDCARD
# route-my1stdeploy   route-my1stdeploy-ocp-learning.apps-crc.testing          svc-my1stdeploy   8080                 None

# 現在実行されている Deployment 名 my1stdeploy の Pod のレプリカ数を 1 から 2 に変更する
oc scale deployment my1stdeploy --replicas 2

# 出力例:
# deployment.apps/my1stdeploy scaled

# スケールされた Deployment の Pod の数を確認する
oc get deployment

# 出力例:
# NAME          READY   UP-TO-DATE   AVAILABLE   AGE
# my1stdeploy   2/2     2            2           43m
# deployment.apps/my1stdeploy scaled

# スケールされた Deployment の Pod を確認する (1回目)
oc get pods

# 出力例:
# NAME                           READY   STATUS    RESTARTS   AGE
# my1stdeploy-6cb9b94896-9wcs7   1/1     Running   0          6m47s　←　AGEが6分強、新しく展開された2つ目のPod
# my1stdeploy-6cb9b94896-r5zdb   1/1     Running   0          50m　　←　AGEが50分、元から動いていた1つ目のPod

# 現在実行されている Deployment 名 my1stdeploy の Pod のレプリカ数を 2 から 5 に変更する
oc scale deployment my1stdeploy --replicas 5

# 出力例:
# deployment.apps/my1stdeploy scaled ←　レプリカ数を1から2にしたときと同じ出力

# スケールされた Deployment の Pod を確認する (2回目)
oc get pods

# 出力例:
# NAME                           READY   STATUS    RESTARTS   AGE
# my1stdeploy-6cb9b94896-42xhk   1/1     Running   0          10s
# my1stdeploy-6cb9b94896-9wcs7   1/1     Running   0          37m
# my1stdeploy-6cb9b94896-kspd5   1/1     Running   0          10s
# my1stdeploy-6cb9b94896-r5zdb   1/1     Running   0          80m
# my1stdeploy-6cb9b94896-smsfb   1/1     Running   0          10s

# 5つの Pod による Deployment 構成の場合のエンドポイントを確認する
# (ENDPOINTSには5つのPod IPが表示されるが、幅が足りず省略されている)
oc get endpoints

# 出力例:
# NAME              ENDPOINTS                                                           AGE
# svc-my1stdeploy   10.217.0.101:8080,10.217.0.118:8080,10.217.0.119:8080 + 2 more...   60m

# 上記のルートにより公開されたPod (my1stdeploy) にWebアクセスができるかを試す
# (firefoxなどのブラウザでアクセスしても良い)
curl route-my1stdeploy-ocp-learning.apps-crc.testing

# 出力例: (以下はコンテナイメージ内のWebページ)
# <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">
# <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
#   <head>
#     <title>Test Page for the HTTP Server on Red Hat Enterprise Linux</title>
#     <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
#     <style type="text/css">
#       /*<![CDATA[*/
