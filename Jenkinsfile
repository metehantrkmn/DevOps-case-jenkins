pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS = credentials('34e219f7-0065-448a-8aa2-4a01d4f16db6')
        DOCKER_IMAGE = 'metehan1171/devops-case:latest'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/metehantrkmn/DevOps-case-jenkins.git'
            }
        }

        stage('Build and Push') {
            steps {
                script {
                    // Build image
                    sh "docker build -t ${DOCKER_IMAGE} ."
                    
                    // Docker Hub login
                    withCredentials([usernamePassword(credentialsId: '34e219f7-0065-448a-8aa2-4a01d4f16db6', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh '''
                            echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                            docker push ${DOCKER_IMAGE}
                        '''
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig([
                    credentialsId: 'kubeconfig',
                    contextName: 'jenkins-context'
                ]) {
                    sh '''
                        # Verify connection
                        kubectl config current-context
                        kubectl get nodes
                        
                        # Apply manifests
                        kubectl apply -f kubernetes/deployment.yaml
                        kubectl apply -f kubernetes/service.yaml
                        kubectl apply -f kubernetes/ingress.yaml
                        
                        # Verify deployment
                        kubectl get pods
                    '''
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed.'
        }
    }
}