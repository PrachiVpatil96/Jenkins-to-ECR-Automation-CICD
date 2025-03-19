pipeline {
    agent any
    tools {
        jdk 'Jdk-17'
        maven 'Maven'
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

        // stage('SonarQube Analysis') {
        //     steps {
        //         withCredentials([string(credentialsId: 'sonar-credentials', variable: 'SONAR_TOKEN')]) {
        //             sh '''
        //                 mvn clean verify sonar:sonar \
        //                 -Dsonar.projectKey=spc \
        //                 -Dsonar.host.url=https://13.233.168.85:9000 \
        //                 -Dsonar.login=$SONAR_TOKEN
        //             '''
        //         }
        //     }
        // }

        stage('Build Docker Image') {
            steps {
                sh "docker image build -t spc:$IMAGE_TAG ."
            }
        }

        stage('Login to AWS ECR') {
            steps {
                withCredentials([
                    string(credentialsId: 'aws-credentilas', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'aws-credentilas', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh """
                    export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                    export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                    aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO
                    """
                }
            }
        }

        stage('Tag and Push to ECR') {
            steps {
                sh "docker image tag spc:$IMAGE_TAG $ECR_REPO:$IMAGE_TAG"
                sh "docker image push $ECR_REPO:$IMAGE_TAG"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh "kubectl apply -f manifest/."
            }
        }

        
    }
}
