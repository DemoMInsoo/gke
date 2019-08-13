Kubernetes Engine & Cloud Run ハンズオン　トレーニング コマンド集
============================================================

##### 使用するプロジェクトを選択
```text
export PROJECT_ID=[ ご使用するプロジェクトIDを指定 ]
gcloud config set project $PROJECT_ID
```
<br/>

##### 使用するリージョンとゾーンを選択
```text
gcloud config set compute/zone asia-northest1-a
```
<br/>

#### Kubernetes クラスタを作成する
```text
gcloud container clusters handson-[受験者の英語のお名前]
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