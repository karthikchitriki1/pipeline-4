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
        //tage('Build') {
           steps {
            sh '"mvn" -Dmaven.test.failure.ignore clean install'
              }
        }
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
                sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
                sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
         }
        }
    }
       //tage("Deploy to EKS") {
            steps{
                withCredentials([string(credentialsId: 'kubess', variable: 'kubes')]) {
                    sh 'aws eks update-kubeconfig --name demo-eks --region ap-south-1'
                    sh '''if /usr/local/bin/kubectl get deploy | grep tomcat
                    then
                    /usr/local/bin/kubectl set image deployment tomcat= 536009196338.dkr.ecr.ap-south-1.amazonaws.com/tomcat:latest
                    /usr/local/bin/kubectl rollout restart deployment tomcat
                    else
                    /usr/local/bin/kubectl apply -f deployment.yml
                    fi'''
             }            
            }
        }
       //tage("Wait for Deployments") {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    sh '/usr/local/bin/kubectl get svc'
                }
            }
        }  
    }
}
