pipeline {
	agent any
	environment {
		VERSION = '3.0.3'
		DOCKERHUB_CREDENTIALS = credentials('epal135')
	}
	stages {
		stage('Build') {
			steps {
				sh 'echo "building the FlaskDemo v.${VERSION} from Git repo"'
			}
        	}

        	stage('Test') {
            		steps {
				sh """
					export PATH=/usr/local/bin:$PATH
					python3 -m venv venv
					source venv/bin/activate
					python -m pip install --upgrade pip
					pip install --upgrade pip
					pip install -r requirements.txt
					python -m pytest
				"""	
            		}
        	}

		stage('Build Docker Image')
                {
                        steps {
				sh 'sudo chmod 666 /var/run/docker.sock'
                                sh 'docker build -t epal135/flaskdemo:$VERSION .'
                                sh 'docker build -t epal135/flaskdemo:latest .'
                        }
                }

		stage('Dockerhub Login') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}

		stage('Push Image to Dockerhub') {

			steps {
				sh 'docker push epal135/flaskdemo:$VERSION'
				sh 'docker push epal135/flaskdemo:latest'
			}
		}

        	stage('Deploy')
        	{
           		steps {
				echo "deploying the application"
            		}
        	}
    	}
    	post {
        	always {
			echo 'The pipeline completed'
        	}
        	success {
			echo "FlaskDemo Application Docker Image v.${VERSION} Built and Pushed to DockerHub Up!!"
			sh 'docker logout'
        	}
        	failure {
			echo 'Build stage failed'
			error('Stopping early…')
        	}
   	}
}

