pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS = credentials('34e219f7-0065-448a-8aa2-4a01d4f16db6')
        KUBECONFIG = '~/.kube/config'
        DOCKER_IMAGE = 'metehan1171/devops-case:latest'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/metehantrkmn/DevOps-case-jenkins.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                    echo $DOCKER_CREDENTIALS_PSW | docker login -u $DOCKER_CREDENTIALS_USR --password-stdin
                    docker push ${DOCKER_IMAGE}
                    docker logout
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    kubectl apply -f kubernetes/deployment.yaml
                    kubectl apply -f kubernetes/service.yaml
                    kubectl apply -f kubernetes/ingress.yaml
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed.'
        }
        always {
            sh 'docker logout'
        }
    }
}