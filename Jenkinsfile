pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="536009196338"
        AWS_DEFAULT_REGION="ap-south-1"
        IMAGE_REPO_NAME="tomcat"
        IMAGE_TAG="latest"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }
   
    stages {
         stage('installing helm') {
            steps {
                 sh '''if /usr/local/bin/kubectl helm version
                 then
                 helm repo list
                 else
                 curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
                 chmod 700 get_helm.sh
                 ./get_helm.sh
                 helm version
                 fi'''
            }
         }
    }
}
        
        
