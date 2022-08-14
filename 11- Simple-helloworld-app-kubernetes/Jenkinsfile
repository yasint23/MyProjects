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