pipeline {
    agent any
    environment {
      AWS_REGION = 'eu-north-1'
      AWS_ACCOUNT_ID = '887793660251'
      ECR_REPOSITORY = 'inventory-service-repo'
      IMAGE_TAG = "${BUILD_NUMBER}"
      ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
      IMAGE_NAME = "${ECR_REGISTRY}/${ECR_REPOSITORY}:${IMAGE_TAG}"
    }
    stages{
      stage('build docker image') {
        steps {
          sh 'docker build -t ${IMAGE_NAME} .'
        }
      }
      stage('ECR Login') {
        steps {
          withCredentials([usernamePassword(
            credentialsId: 'aws-creds',
            usernameVariable: 'AWS_ACCESS_KEY_ID',
            passwordVariable: 'AWS_SECRET_ACCESS_KEY'
          )]) 
          {
            sh '''
               export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
               export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
               export AWS_DEFAULT_REGION=${AWS_REGION}
               aws ecr get-login-password --region ${AWS_REGION} | \
               docker login --username AWS --password-stdin ${ECR_REGISTRY}
            '''
          }
        }
      }
    
      stage('Push Image') {
        steps {
            sh 'docker push $IMAGE_NAME'
        }
      }
    }
}    

