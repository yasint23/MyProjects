# Hello-python project deployment by kubernetes

Simple hello world python Flask application.

- Firstly I created docker file then to see the application I prepared simple Flask application. 
- I created `main.py` and `requirement.txt` inside of `app` folder.
- `Dockerfile` is inside of docker folder. 
- I push the files to my Guthub account.

- I create `deployment.yaml` file to deploy application by kubernetes cluster and push to the github repository.

- I log in the jenkins server which I created by terraform file. 
- I create credentials named `dockerhub` in jenkins to reach docker hub for pulling image by jenkins pipeline.
- I create `ssh-agent` for kubernetes commands in jenkins file. 
- Finally I created `Jenkinsfile` and push it to my github repository.

- Jenkins pipeline stages;
