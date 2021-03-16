# cicd-jenkins

CICD Pipeline to deploy different applications, the process checks the app installed, removes the app and install the next app to install in the list. 

## About the process:
- Create a virtual machine and install Jenkins, Docker, Helm, and kubectl on it. 
- In this CICD process you have these stages:
  - Pull the code of the next app in the list of apps. 
  - Build a docker image and upload the image to [Dockerhub](https://hub.docker.com/u/gastonkanze).
  - Deploy to K8S using [healmchart](https://github.com/GastonKanze/cicd-jenkins-helmchart.git) .


## Screenshots: 

###### Pipeline:
![image](https://user-images.githubusercontent.com/12170121/111260528-a716c300-8629-11eb-9ea3-210b5460ec7f.png)


###### Jenkins credentials:
![image](https://user-images.githubusercontent.com/12170121/111260546-b3028500-8629-11eb-89bd-e4cf04ec7852.png)


###### K8S:
![image](https://user-images.githubusercontent.com/12170121/111260578-c281ce00-8629-11eb-812c-88e293c108f0.png)


###### Dockerhub: 
![image](https://user-images.githubusercontent.com/12170121/111260614-d299ad80-8629-11eb-922a-c2fc21cfd066.png)


###### Application:
Application 1:

![image](https://user-images.githubusercontent.com/12170121/111260691-f5c45d00-8629-11eb-88ab-43bece9d9e30.png)

Application 2:

![image](https://user-images.githubusercontent.com/12170121/111260801-21474780-862a-11eb-94f2-ff24c80d78cd.png)


## How to install Jenkins in a server?:
- [Documentation](https://www.techrepublic.com/article/how-to-install-jenkins-on-ubuntu-server-18-04/)

## How to install Docker in a server?:
- [Documentation](https://docs.docker.com/engine/install/ubuntu/)

## How to install kubectl in a server?:
- [Documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)

## How to install Helm in a server?:
- [Documentation](https://phoenixnap.com/kb/install-helm)
