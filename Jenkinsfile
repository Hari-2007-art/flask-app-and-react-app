pipeline {
    agent any
    
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-creds')
        DOCKER_USERNAME = "${env.DOCKER_HUB_CREDENTIALS_USR}"
        FLASK_IMAGE ="${env.DOCKER_USERNAME}/flask-app"
        REACT_IMAGE ="${env.DOCKER_USERNAME}/react-app"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Cloning repository'
                checkout scm
            }
        }
  
        stage('BUILD FLASK-IMAGE') {
            steps {
                sh 'docker build -t $FLASK_IMAGE:latest ./flask-app'
            }
        }

        stage('BUILD REACT-IMAGE') {
            steps {
                sh 'docker build -t $REACT_IMAGE:latest ./react-app'
            }
        }

        stage('Push Images to Docker HUB') {
            steps {
                sh '''
                    echo $DOCKER_HUB_CREDENTIALS_PSW | docker login \ 
                    -u $DOCKER_HUB_CREDENTIALS_USR --password-stdin
                    docker push $FLASK_IMAGE:latest
                    docker push $REACT_IMAGE:latest
                   '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    cd ~/Docker
                    docker-compose down
                    docker-compose up -d --build
                   '''
            }
        }
    }

    post {
        success {
            echo 'App deployed'
        }
        failure {
            echo 'Failed : Some Error'
        }
    }
}
        
 
