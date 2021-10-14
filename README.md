# mongo-express-kube

## 概要
Kubernetes上でMongo Expressを立ち上げ、稼働させるための概要説明です。  
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

## AION における Mongo Express のデプロイ・稼働
[aion-core-manifests](https://github.com/latonaio/aion-core-manifests)の template/bases/mongo-express/deployment.yml に Mongo Express をデプロイ・稼働させるために必要なymlファイルが配置されています。

## ymlファイル（deployment.yml）の中身  
ymlファイル（deployment.yml）の中身  

```      
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo-express-deployment
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
        - name: mongo-express
          image: mongo-express

          env:
            - name: MONGODB_USER
              value: root
            - name: MONGODB_PASS
              value: root
          ports:
            - name: mongo-express
              containerPort: 8081
---
apiVersion: v1
kind: Service
metadata:
  name: mongo-express-service
spec:
  selector:
    app: mongo-express
  type: LoadBalancer
  ports:
    - name: mongo-express
      port: 8081
      protocol: TCP
      targetPort: 8081
      nodePort: 32767
```  

### GUIで操作する方法
http://localhost:8081 にアクセスする。
