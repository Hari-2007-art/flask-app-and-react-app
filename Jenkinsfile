pipeline {
    agent any
    
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-creds')
        DOCKER_USERNAME = "${env.DOCKER_HUB_CREDENTIALS_USR}"
        FLASK_IMAGE ="${env.DOCKER_USERNAME}/flask-app"
        REACT_IMAGE ="${env.DOCKER_USERNAME}/react-app"
    }

    stages {
        stage('Checkout stage') {
            steps {
                echo 'Cloning repository from github'
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
                    docker login -u $DOCKER_HUB_CREDENTIALS_USR -p $DOCKER_HUB_CREDENTIALS_PSW
                    docker push $FLASK_IMAGE:latest
                    docker push $REACT_IMAGE:latest
                   '''
            }
        }

        stage('Deploying to minikube') {
            steps {
                 sh '''
                    export KUBECONFIG=/var/jenkins_home/.kube/config

                    kubectl apply -f K8ss/loki.yml
                    kubectl apply -f K8ss/promtail.yml
                    kubectl apply -f K8ss/grafana.yml

                    kubectl apply -f K8ss/flask-deployment.yml
                    kubectl apply -f K8ss/deployment.yml

                    kubectl rollout restart deployment/flask-app
                    kubectl rollout restart deployment/react-app

                    kubectl rollout status deployment/flask-app
                    kubectl rollout status deployment/react-app
                '''
            }
        }
    }

    post {
        success {
            echo 'App deployed successfully'
        }
        failure {
            echo 'Failed : Some Error'
        }
    }
}
        
 
