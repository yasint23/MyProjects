# Full Microservices CI/CD Project 

- I worked mainly on a micro-services application fully automated, a Dockerized Web Application developed in Java Springboot and integrated with MySQL database.

![Spring Petclinic Microservices screenshot](9-Full-Microservices-CI-CD-Project\docs\application-screenshot.png)

![Spring Petclinic Microservices architecture](9-Full-Microservices-CI-CD-Project/docs/microservices-architecture-diagram.jpg)

- Jenkins used as the CI/CD Pipelines and deployment on Kubernetes cluster with monitoring. 

![Pipeline Configuration](9-Full-Microservices-CI-CD-Project/docs/pipeline-config.png)

![Nightly Test Pipeline](9-Full-Microservices-CI-CD-Project/docs/nightly-pipeline.png)

- We created all the infrastructure on AWS EC2 Service. Also, we used Git as the version control system during the whole process. 
We prepared base branches namely master, dev, and release for the DevOps cycle.
The code was developed in Java and Maven was used as the build tool. Maven Wrapper used for the testing, packaging, and installing phases. 

- I spun up the development server through a CloudFormation template. We prepared Dockerfiles for each microservices and, we also prepared a Docker Compose file to deploy the application with Docker-Swarm.

![Project Structure](11-Full-Microservices-CI-CD-Project/docs/structure.png)

- We also used Rancher for creating, controlling, and monitoring Kubernetes clusters for staging and production environments. We used Jenkins pipeline scripts to deploy the application to both staging and production environments. 

![Rancher](9-Full-Microservices-CI-CD-Project/docs/rancher.png)

![Prometheus Metrics](9-Full-Microservices-CI-CD-Project/docs/prometheus.png)




