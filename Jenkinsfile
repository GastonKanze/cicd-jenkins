#!/groovy

def imageName
def appPath
//Array of applications ["Repository", "Folder name", "Application name"]
def repositories = [["https://github.com/taniarascia/react-tutorial.git","react-tutorial","app1"],["https://github.com/aditya-sridhar/simple-reactjs-app.git","simple-reactjs-app", "app2"] ]
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
                          sh "microk8s.kubectl get svc ${repositories[i][2]}"
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
                    // Saving config
                    sh "microk8s.kubectl config view --raw > $HOME/.kube/config"
                    // Checking Helm repo
                    /*try{
                       sh "helm repo list"
                    } catch(Exception x){
                       sh "helm repo add stable https://charts.helm.sh/stable"
                    }*/
                    // Cloning helm chart repository
                    sh "git clone https://github.com/GastonKanze/cicd-jenkins-helmchart.git"
                    dir("cicd-jenkins-helmchart"){
                        // Install application
                        sh "helm upgrade --install kube-chart . --set appName=${imageName}"
                    }
                    echo "Service is pointing to the IP:"
                    sh "microk8s.kubectl get service/${imageName} -o jsonpath='{.spec.clusterIP}'"
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
