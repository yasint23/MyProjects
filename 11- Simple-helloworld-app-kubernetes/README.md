# hello-python
Very simple hello world python Flask application.
Önce aşagıda göndermiş olduğum Docker file yazdım.Sonra bu uygulmayı görmek için basit bir  Flask uygulması üzerinden yapıyı kurmuş oldum. app için içinde main.py ve requirement.txt dosyası,Docker file docker klösöründe olacak şekilde Github adresinde oluşturduğum repoma push ettim.
Docker File 
FROM python:3.7

RUN mkdir /app
WORKDIR /app
ADD . /app/
RUN pip install -r requirements.txt

EXPOSE 5000
CMD ["python", "/app/main.py"]
------------------------------------------------------------
 main.py 
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"

if __name__ == "__main__":
    app.run(host='0.0.0.0'
----------------------------------------------
Uygulamayı Kubernetes cluster üzerinden deploy edebilmek basit Deployment yaml dosyası oluşturdum ve aynı şekilde bunu repoya gönderdim.
Deployment.yaml
apiVersion: v1
kind: Service
metadata:
  name: hello-world-service
spec:
  selector:
    app: hello-world
  ports:
  - protocol: "TCP"
    port: 6000
    targetPort: 5000
  type: LoadBalancer


---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: hello-world
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      containers:
      - name: hello-world
        image: hasant23/hello-world:latest
        imagePullPolicy: Never
        ports:
        - containerPort: 5000
------------------------------------------------------------
Terraform ile oluşturdğum jenkins server'a ayarları yaparak bağlandım.Dockerhub üzerinden jenkins server dan login olup işlem yapabilmek için "dockerhub" isimli credentials oluşturdum.Ayrıca kubernetes cluster için "ssh-agent" oluşturdum.
sonrasında Jennkins file yazarka githup adresine gönderdim.
Jenkinsfile
pipeline{

	agent any

	environment {
		DOCKERHUB_CREDENTIALS=credentials('dockerhub')
	}

	stages {

		stage('Build') {

			steps {
				sh 'docker build -t hasant23/hello-world:latest .'
			}
		}
	}
		stage('SonarQube analysis') {
			steps {
				withSonarQubeEnv('sonarqube-container') 
					
				}
			}
		
		stage("Quality Gate") {
			steps {
				waitForQualityGate abortPipeline: true
			}
		}

		stage('Login') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}


		stage('Push') {

			steps {
				sh 'docker push hasant23/hello-world:latest'
			}
		}


		stage('Deploy to K8s')
		{
			steps{
				sshagent(['k8s-jenkins'])
				{
					sh 'scp -r -o StrictHostKeyChecking=no node-deployment.yaml username@102.10.16.23:/path'
					
					script{
						try{
							sh 'ssh username@102.10.16.23 kubectl apply -f /path/node-deployment.yaml --kubeconfig=/path/kube.yaml'

							}catch(error)
							

							}
					}
				}
			}
		}
	}

	post {
		always {
			sh 'docker logout'
		}
	}

	}
--------------------------------------------------------------------------
Jnekins server üzerinden pipeline job oluşturarak aşağıdaki sıralı işlem gerçekleştirildi.
Jenkinsfile kısmında ilk olarak Dockerfile ile "hasant23/hello-world:latest" isimli image build etmiş oluyoruz.Sonarqube son yıllarda şirketler tarafından kullanılan code build işleminin detaylarını görebilme ve developer için görsel rapor sunan uygulama için Jenkins server üzerinden sonarqube plugin yükleyerek ayrıca configrasyon işlemleri yapıldı. Oluşturulan image Dockerhub login olarak push edilme stepleri yapıldı.Son olarak yine terraform ile oluştrulan Kubernetes cluster veya minikube üzerinden var olan "Deployment yaml" dosyasını scp(securecopy) ile tanımladığımız yol ve Ip ile Masternode gönderiyoruz.Sonrasında kaynaklarla gönderdiğimiz deployment yaml üzerinden kubectl komutları ile deploy etmiş oluyoruz.Deployment yaml da image olarak Dockerhub tan göndermiş olduğumuz image çekiyoruz. Daha sonra uygulmayı tanımladığımız port üzerinden görebiliriz.
