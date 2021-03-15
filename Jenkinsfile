#!/groovy

def imageName
def repositories = [["https://github.com/aditya-sridhar/simple-reactjs-app.git","app1"], ["https://github.com/angelourian/simple-reactjs-app.git", "app2"], ["https://github.com/Kornil/simple-react-app.git", "app3"]]

pipeline {
    agent any
    options {
        timestamps()
        timeout(time: 1, unit: 'HOURS')
    }
    stages {
        stage('Pull code') {
            steps {
                script {
                    imageName = repositories[0][1]
                    sh "git clone ${repositories[0][0]}"
                }
            }
        }
        
        stage('Build image') {
            steps {
                script {
                    sh "git clone https://github.com/GastonKanze/react-app.git"
                    sh "cp react-app/Dockerfile simple-reactjs-app"
                    dir("simple-reactjs-app") {
                        sh "docker build -t ${imageName}:${BUILD_NUMBER} ."
                    }
                }
            }
        }
        
        stage('Push image') {
            steps {
                script {
                    dockerHubRepo = "gastonkanze/${imageName}"
                    withCredentials([usernamePassword(credentialsId: 'DOCKER-ID', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh "docker login -u ${USERNAME} -p ${PASSWORD}"
                        sh "docker tag ${imageName}:${BUILD_NUMBER} ${dockerHubRepo}:${BUILD_NUMBER}"
                        sh "docker tag ${imageName}:${BUILD_NUMBER} ${dockerHubRepo}:latest"
                        sh "docker push ${dockerHubRepo}:${BUILD_NUMBER}"
                        sh "docker push ${dockerHubRepo}:latest"
                    }
                }
            }
        }
        
        stage('Deploy to K8S') {
            steps {
                script {
                    sh "microk8s.kubectl apply -f react-app/simple-react-app-service.yml"
                    echo "Service is pointing to the IP:"
                    sh "microk8s.kubectl get service/simple-react-app -o jsonpath='{.spec.clusterIP}'"
                }
            }
        }
        
    }

    post {
        cleanup {
            cleanWs()
        }
        success {
            script {
                echo "Build success"
            }
        }
        failure {
            script {
                echo "Build failed"
            }
        }
        unstable {
            script {
                echo "Build unstable"
            }
        }
    }
}
