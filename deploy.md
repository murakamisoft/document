
# Spring Boot アプリの SDI 環境へのデプロイ手順

この手順書では、Spring Boot アプリを SDI（Smart Data Integration）環境にデプロイするための流れを説明します。

---

## 手順概要

1. ローカルPCでアプリ開発＆ビルド
2. コンテナイメージ作成＆レジストリにPush
3. SDI環境で namespace 作成
4. Deployment/Service のマニフェスト準備
5. SDI環境にデプロイ

---

## 1. ローカルPCでアプリ開発＆ビルド

- **場所**: ローカルPC（Windows）
- **内容**: Spring Boot アプリの開発、Gradleを使用してビルドします。

```bash
./gradlew build
```

- ビルド後、`build/libs/order-api.jar` などの JAR ファイルが生成されます。

---

## 2. コンテナイメージ作成＆レジストリにPush

- **場所**: ローカルPC（Windows）または CI環境
- **内容**: Dockerイメージを作成し、レジストリにPushします。

### Dockerイメージの作成

```bash
docker build -t your-registry/order-api:1.0 .
```

### イメージのPush

```bash
docker push your-registry/order-api:1.0
```

> `your-registry` は使用するレジストリの名前です（例: Docker Hub, Harbor, Quayなど）。

---

## 3. SDI環境で namespace 作成

- **場所**: SDI環境（Kubernetes or OpenShift）  
  もしくはローカルPCからリモート接続して実行

### コマンド

```bash
kubectl create namespace order-management-dev
```

または OpenShiftの場合:

```bash
oc new-project order-management-dev
```

> `order-management-dev` は好きな名前に変更可能です。

---

## 4. Deployment/Service のマニフェスト準備

- **場所**: ローカルPC（Windows）
- **内容**: Kubernetesの `Deployment` と `Service` のマニフェストファイルを作成します。

### `deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-api
  namespace: order-management-dev
spec:
  replicas: 1
  selector:
    matchLabels:
      app: order-api
  template:
    metadata:
      labels:
        app: order-api
    spec:
      containers:
        - name: order-api
          image: your-registry/order-api:1.0
          ports:
            - containerPort: 8080
```

### `service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: order-api-service
  namespace: order-management-dev
spec:
  selector:
    app: order-api
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

---

## 5. SDI環境にデプロイ

- **場所**: SDI環境（Kubernetes or OpenShift）  
  もしくはローカルPCからリモート接続して実行

### コマンド

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

または OpenShiftの場合:

```bash
oc apply -f deployment.yaml
oc apply -f service.yaml
```

---

## 追加確認

- **Podの確認**: デプロイが成功したか確認します。

```bash
kubectl get pods -n order-management-dev
```

- **Podのログ**: アプリのログを確認します。

```bash
kubectl logs <pod-name> -n order-management-dev
```

---

## 補足情報

- SDI環境にアクセスするためには、適切な `kubeconfig` または `oc` CLI の設定が必要です。
- CI/CD パイプラインがある場合、これらの手順を自動化できます。

