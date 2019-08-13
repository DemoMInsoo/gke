Kubernetes Engine & Cloud Run ハンズオン　トレーニング コマンド集
------------------------------------------------------------

##### 使用するプロジェクトを選択
```text
export PROJECT_ID=[ ご使用するプロジェクトIDを指定 ]
gcloud config set project $PROJECT_ID
```
<br/>

##### 使用するリージョンとゾーンを選択
```text
gcloud config set compute/zone asia-northeast1-a
```
<br/>

#### Kubernetes クラスタを作成する
```text
gcloud container clusters create handson-[ 受験者の英語のお名前 ] --num-nodes=2
```
<br/>

#### Webアプリ開発用のフォルダを作成する
```text
mkdir handson0920
cd handson0920
```
<br/>

#### 簡単なPythonのWebアプリをコーディングする
```text
nano app.py
```
<pre><code>
from flask import Flask
app = Flask(__name__)

@app.route('/')
def index():
  return 'Hello World'
  
app.run(host='0.0.0.0', port=8080)  
</code></pre>
```text
Ctrl + x で変更内容を保存後終了
```
<br/>

#### Dockerfileを作成する
```text
nano Dockerfile
```
<pre><code>
FROM python:3.5-slim
COPY . /
RUN pip install flask
EXPOSE 8080
CMD ["python","app.py"]
</code></pre>
```text
Ctrl + x で変更内容を保存後終了
```
<br/>

#### cloudbuildファイルを作成する
```text
nano cloudbuild.yaml
```
```yaml
steps:
- name: 'gcr.io/cloud-builders/docker'
  args: [ 'build', '-t', 'gcr.io/$PROJECT_ID/helloapp-[ 受験者の英語のお名前 ]:latest', '.']
images:
- 'gcr.io/$PROJECT_ID/helloapp-[ 受験者の英語のお名前 ]'
```
```text
Ctrl + x で変更内容を保存後終了
```
<br/>

#### Cloud BuildでDockerイメージをビルドして、Container Registryにプッシュする
```text
gcloud builds submit --config cloudbuild.yaml .
```
<br/>

#### Container Registryのイメージ一覧を表示する
```text
gcloud container images list
```
<br/>

#### Kubernetes Podを作成する(Webアプリの稼働)
```text
kubectl run hello-app --image=gcr.io/$PORJECT_ID/helloapp-[ 受験者の英語のお名前 ]E:latest --port 8080 --replicas=5
```
<br/>

#### Kubernetes Serviceを作成する（外部からのエンドポイントを作成）
```text
kubectl expose deployment hello-app --type=LoadBalancer --port 80 --target-port 8080
```
<br/>

#### Kubernetes Service一覧を表示する
```text
kubectl get service
```
<br/>

#### Kubernetes Pod一覧を表示する
```text
kubectl get pod -o wide
```
<br/>

#### Kubernetes Node一覧を表示する
```text
kubectl get node
```
<br/>


#### Google Compute Engineのインスタンス一覧を表示する
```text
gcloud compute instances list
```
<br/>

#### 一旦作成したDeploymentとServiceを削除する
```text
kubectl delete deployment hello-app
kubectl delete service hello-app
```
<br/>

#### デプロイメント定義ファイルを作成する
```text
nano kube-deploy.yaml
```
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello-app
  template:
    metadata:
      labels:
        app: hello-app
    spec:
      containers:
      - name: hello-app
        image: gcr.io/[PROJECT_ID]/helloapp-[ 受験者の英語のお名前 ]:latest
        port:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: hello-app
spec:
  type: LoadBalancer
  selector:
    app: hello-app
  ports:
  - name: http
    port: 80
    targetPort:8080
```
```text
Ctrl + x で変更内容を保存後終了
```
<br/>

#### Kubernetesにデプロイメント定義ファイルを適用する
```text
kubectl apply -f kube-deploy.yaml
```
<br/>

#### Pod一覧を表示
```text
kubectl get pod
```
<br/>

####  定義ファイルのレプリカ数の指定を１から３へ変更する
```text
nano kube-deploy.yaml
```
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: hello-app
```
<br/>

#### Kubernetesに変更したデプロイメント定義ファイルを適用する
```text
kubectl apply -f kube-deploy.yaml
```
<br/>

#### Pod一覧を表示する
```text
kubectl get pod
```
<br/>

#### Cloud Runでアプリを稼働させる
```text
gcloud beta run deploy hello-run-app-[ 受験者の英語のお名前 ] --image gcr.io/$PROJECT_ID/helloapp-[ 受験者の英語のお名前 ]:latest --region us-central1
```

#### 作成した Cloud Runのサービス情報を表示する
```text
gcloud beta run services describe hello-run-app-[ 受験者の英語のお名前 ] --region us-central1
```