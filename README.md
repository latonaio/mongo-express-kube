# mongo-express-kube

## 概要
Kubernetes上でMongo Expressを立ち上げるためのマイクロサービスです。  
Mongo Expressとは、MongoDBをGUIで操作するためのツールです。   
MongoDBについては、[こちら](https://github.com/latonaio/mongodb-kube) をご覧ください。

mongo-express-kubeには、立ち上げに必要なマニフェストファイルが入っています。

## 動作環境
mongo-express-kubeは、kubernetes上での動作を前提としています。   
kubernetesの環境構築後に起動してください。

## マニフェストファイルの仕様
* ポート: 8081   
* nodePort: 32767   
* コンテナイメージ: mongo-express:latest   

## 起動方法
### 環境変数
|環境変数|値|   
|:---|:---|    
|MONGODB_USER|mongoDBのユーザー名|   
|MONGODB_PASS|mongoDBのパスワード|   

### 単独で立ち上げる場合
```
kubectl apply -f k8s/deployment.yml
```

### デプロイ on AION
aion-core-manifests/generated/default.yml に以下のように追記してください。   
環境変数は適宜置き換えてください。 
```
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo-express-deployment
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo-express
  template:
    metadata:
      labels:
        app: mongo-express
    spec:
      containers:
      - env:
        - name: MONGODB_USER
          value: MONGODB_USER_XXX
        - name: MONGODB_PASS
          value: MONGODB_PASSWORD_XXX
        image: mongo-express
        name: mongo-express
        ports:
        - containerPort: 8081
          name: mongo-express
---
apiVersion: v1
kind: Service
metadata:
  name: mongo-express-service
  namespace: default
spec:
  ports:
  - name: mongo-express
    nodePort: 32767
    port: 8081
    protocol: TCP
    targetPort: 8081
  selector:
    app: mongo-express
  type: LoadBalancer
```

### GUIで操作する方法
http://localhost:8081 にアクセスする。
