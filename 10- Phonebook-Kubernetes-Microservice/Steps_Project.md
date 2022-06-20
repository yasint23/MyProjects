## Steps for Project:

- Run kubernetes-env-cf.yaml file on AWS console cloudformation page.

- We need three services;
* Deployment for search, 
* Deployment for add/update/delete,
* Deployment for Mysql and Persistent volume will be connected to it

1- Connect to the Master Node. (Update and install git)

2- Pull project support files from github repo.

3- Create 'Dockerfile' inside folder 'image_for_web_server' then create docker container and push to docker hub.
$ docker build -t yasint23/web_server . 
$ docker login
username:
password:  
$ docker push yasint23/web_server

4- Create same 'Dockerfile' inside folder 'image_for_result_server' then create docker container and push to docker hub.
$ docker build -t yasint23/result_server . 
$ docker push yasint23/result_server

5- Create folder 'Solution' / 'mysql_deployment' / 'result_server' / 'web_server' / secrets_configMap

6- Go to 'Solution' / 'mysql_deployment'
- Create persistent_volume.yml 
- Create persistent_volume_claim.yml 
$ kubectl apply -f .
$ kubectl get pv,pvc

7- Create mysql_deployment.yaml     (Content from kubernetes.io search "deployment" or install extension of kubernetes to vscode type deployment)

- Create 'mysql_secret.yaml' and 'secrets_configmap.yaml' files under 'secrets_configMap' folder to complete "env" section of 'mysql_deployment.
- Create mysql_service.yaml 

- Run the command under 'secrets_configmap' folder for the creating secrets and config-maps;
$ kubectl apply -f .

- Run the command under 'mysql_deployment' folder for creating service and deployment;
$ kubectl apply -f .
$ kubectl get pod     # One pod must be running
$ kubectl get svc

8- Create 'web_server_deployment.yaml' file under 'web_server' folder. 
Another way to create this file by the command;
$ kubectl create deployment --image=yasint23/web_server web_server --dry-run=client -o yaml > web_server_deployment.yaml

- Create 'web_server_service.yml' file.

- Run the command under 'web_server' folder for creating service and deployment;
$ kubectl apply -f .

9-Create 'result_server_deployment.yaml' file under 'result_server' folder. 
- Create 'result_server_service.yml' file.

- Run the command under 'result_server' folder for creating service and deployment;
$ kubectl apply -f . 
$ kubectl get all      #See the all pod and service

10- Open the port on Kubeworker ec2 security group. Edit inbound rule;
-Custom TCP  30001-30002 Anywhere-IP4
- Check the browser http://public-ip:30001 for add and http://public-ip:30002 for search result-server

* We can use one port for both server by the 'ingress'










