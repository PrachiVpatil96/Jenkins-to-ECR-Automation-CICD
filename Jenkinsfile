pipeline {
    agent any
    tools{
        jdk 'Jdk-17'
        maven 'Maven'
        sonar '/opt/sonar-scanner/sonar-scanner-4.7.0.2747-linux/bin'
    }
    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPO = '430118814498.dkr.ecr.ap-south-1.amazonaws.com/spring-pet-clinic'
        IMAGE_TAG = '1.0'

    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/PrachiVpatil96/Jenkins-to-ECR-Automation-CICD.git'
            }
        }
        stage('Sonarqube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonar-credentials', variable: 'SONAR_TOKEN')]) {
                    sh '''
                       $sonar \
                        -Dsonar.projectKey=spc \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=http://3.109.133.29:9000 \
                        -Dsonar.login=$SONAR_TOKEN
                    '''
        }
    }
}
        stage('Build Image') {
            steps {
                sh "docker image build -t spc:$IMAGE_TAG ."
            }
        }
        stage('Login into ECR') {
            steps {
                sh "aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO"
            }
        }
        stage('Tag and Push to ECR') {
            steps {
                sh "docker image tag spc:$IMAGE_TAG $ECR_REPO:$IMAGE_TAG"
                sh "docker image push $ECR_REPO:$IMAGE_TAG"
            }
        }
    }
}
