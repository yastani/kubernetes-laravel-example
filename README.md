# kubernetes-laravel-example
Docker for Macとhelmとskaffoldでローカル環境にLaravelを構築する

## 手順

### helmの初期化およびnginx-ingressのインストール
```bash
$ $ kubectl config use-context docker-for-desktop
  Switched to context "docker-for-desktop".
$ helm init
$HELM_HOME has been configured at /Users/yastani/.helm.

Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.

Please note: by default, Tiller is deployed with an insecure 'allow unauthenticated users' policy.
To prevent this, run `helm init` with the --tiller-tls-verify flag.
For more information on securing your installation see: https://docs.helm.sh/using_helm/#securing-your-helm-installation
$ helm install --name my-release stable/nginx-ingress
NAME:   my-release
LAST DEPLOYED: Tue May 28 14:21:35 2019
NAMESPACE: default
STATUS: DEPLOYED
(後略)
```

### 自己署名証明書の作成
```bash
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /tmp/nginx-selfsigned.key -out /tmp/nginx-selfsigned.crt; openssl dhparam -out /tmp/sample.pem 2048
Generating a 2048 bit RSA private key
..........................................+++
......+++
writing new private key to '/tmp/nginx-selfsigned.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) []:JP
State or Province Name (full name) []:
Locality Name (eg, city) []:
Organization Name (eg, company) []:
Organizational Unit Name (eg, section) []:
Common Name (eg, fully qualified host name) []:myproject-local.com
Email Address []:
Generating DH parameters, 2048 bit long safe prime, generator 2
This is going to take a long time
$ kubectl create secret tls tls-certificate --key /tmp/nginx-selfsigned.key --cert /tmp/nginx-selfsigned.crt
secret/tls-certificate created
```

### skaffoldを実行
```bash
$ skaffold run
```

## helmとは

helmはkubernetesのパッケージマネージャ。  
パッケージはChart(s)と呼ばれ、アプリケーションを動作させるために必要となるkubernetesの各種リソースを  
ひとまとめに管理し、ツールセットを提供してくれる。  
平たく言うとnpmやaptと概念は一緒。

### Github
https://github.com/helm/helm

## skaffoldとは
kubernetes用のCI/CDワークフローツール。
ローカル環境のアプリケーション開発を促進し、クラウド上のClusterにも自動でデプロイすることが可能。

`skaffold dev` コマンドを使用すれば以下の手順が自動実行される。
- Docker imageをBuildしてContainerを作成
- Container imageをRepositoryにPush
- RepositoryのimageをDeploy

### Github
https://github.com/GoogleCloudPlatform/skaffold

### 公式
https://skaffold.dev/

### インストール手順
https://skaffold.dev/docs/getting-started/#installing-skaffold

## 参考

https://qiita.com/ryosukes/items/4437a50aaf23397605c3