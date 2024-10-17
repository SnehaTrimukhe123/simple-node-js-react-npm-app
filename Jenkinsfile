pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID = '481665085317'
        AWS_DEFAULT_REGION = 'ap-south-1'
        ECR_REPO_NAME = 'nodejs-react-app' // replace with your ECR repository name
        IMAGE_TAG = 'latest'
        AWS_ACCESS_KEY_ID = credentials('aws-access-key')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-key')
        EMAIL = 'snehatrimukhe12@gmail.com'
    }
    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/jenkins-docs/simple-node-js-react-npm-app.git'
            }
        }
        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }
        stage('Login to ECR') {
            steps {
                script {
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials']]) {
                        sh """
                        aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com
                        """
                    }
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t ${ECR_IMAGE} .
                """
            }
        }
        stage('Push Docker Image to ECR') {
            steps {
                sh """
                docker push ${ECR_IMAGE}
                """
            }
        }
    }
    post {
        success {
            mail to: "${EMAIL}",
                 subject: "Jenkins Job '${env.JOB_NAME}' (Build #${env.BUILD_NUMBER}) - Success",
                 body: "Job '${env.JOB_NAME}' has successfully completed."
        }
        failure {
            mail to: "${EMAIL}",
                 subject: "Jenkins Job '${env.JOB_NAME}' (Build #${env.BUILD_NUMBER}) - Failure",
                 body: "Job '${env.JOB_NAME}' has failed."
        }
    }
}
