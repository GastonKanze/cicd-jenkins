#!/groovy

def imageName
def appPath
def repositories = [["https://github.com/aditya-sridhar/simple-reactjs-app.git","simple-reactjs-app", "app1"], ["https://github.com/angelourian/simple-reactjs-app.git", "simple-reactjs-app", "app2"], ["https://github.com/Kornil/simple-react-app.git", "simple-reactjs-app", "app3"]]
def indexToDeploy

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
                    indexToDeploy = 0
                    repositoriesSize = repositories.size()
                    for (def i=0; i< repositoriesSize; i++) {
                        try{
                          sh "microk8s.kubectl get svc ${repo[i][2]}"
                          sh "helm delete kube-chart"
                          if (i+1 == repositoriesSize){
                            indexToDeploy = 0
                          }else{
                            indexToDeploy = i+1
                          }
                          break
                        } catch (Exception x){
                          echo "${repositories[i][2]} is not deployed"
                        }
                    }
                    imageName = repositories[indexToDeploy][2]
                    appPath = repositories[indexToDeploy][1]
                    sh "git clone ${repositories[indexToDeploy][0]}"
                }
            }
        }
        
        stage('Build image') {
            steps {
                script {
                    sh "git clone https://github.com/GastonKanze/cicd-jenkins.git"
                    sh "cp cicd-jenkins/Dockerfile ${appPath}"
                    dir("${appPath}") {
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
                        sh "docker tag ${imageName}:${BUILD_NUMBER} ${dockerHubRepo}:latest"
                        sh "docker push ${dockerHubRepo}:latest"
                    }
                }
            }
        }
        
        stage('Deploy to K8S') {
            steps {
                script {
                    sh "microk8s.kubectl config view > $HOME/.kube/config"
                    sh "git clone https://github.com/GastonKanze/cicd-jenkins-helmchart.git"
                    dir("cicd-jenkins-helmchart"){
                        sh "ls -l"
                        //sh "helm upgrade --install kube-chart . --set appName=${imageName}"
                    }
                    //sh "microk8s.kubectl apply -f react-app/simple-react-app-service.yml"
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
