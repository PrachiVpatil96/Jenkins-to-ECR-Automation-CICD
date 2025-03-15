pipeline {
    agent any
    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPO = '430118814498.dkr.ecr.ap-south-1.amazonaws.com/spring-pet-clinic'
        IMAGE_TAG = '1.0'
    }

    stages{
        stage('Git Checkout'){
            steps{
               git branch: 'main', url: 'https://github.com/PrachiVpatil96/Jenkins-to-ECR-Automation-CICD.git'

             }
        }
        stage('Build Image'){
            steps{
                sh "docker image build -t spc:1.0 ."
            }
        }
        stage('Login into ECR'){
            steps{
                sh "aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO"
            }
        }

        stage('TaG and Push to ECR'){
            steps{
                sh "docker image tag spc:1.0 $ECR_REPO:$IMAGE_TAG"
                sh "docker image push $ECR_REPO:$IMAGE_TAG"
            }
        }
       
    }   
}