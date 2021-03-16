# cicd-jenkins

CICD Pipeline to deploy different applications, the process checks the app installed, removes the app and install the next app to install in the list. 

In this CICD process you have these stages:
- Pull the code of the next app in the list of apps. 
- Build a docker image and upload the image to dockerhub.
- Deploy to K8S using healmchart.
