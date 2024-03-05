pipeline {
    agent any
    
    environment {
        // Define environment variables
        JAVA_HOME = '/usr/lib/jvm/java-11-amazon-corretto.x86_64'
        DOCKER_IMAGE = 'public.ecr.aws/n5h8y2y9/java-app'
        ECR_REGISTRY = 'public.ecr.aws/n5h8y2y9'
        ECR_CREDENTIALS_ID = 'aws'
    }
    stages{
        stage('Build Application'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/surics47/java-automation.git']]])
                sh 'mvn clean install'
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'ls -la'
                    sh 'docker --version'
                    docker.build("${ECR_REGISTRY}/${DOCKER_IMAGE}")
                }
            }
        }
        stage('Login to ECR Public') {
            steps {
                script {
                    sh "aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ECR_REGISTRY}"
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    docker.withRegistry("https://${ECR_REGISTRY}", ECR_CREDENTIALS_ID) {
                        docker.image("${ECR_REGISTRY}/${DOCKER_IMAGE}").push("${env.BUILD_ID}")
                        docker.image("${ECR_REGISTRY}/${DOCKER_IMAGE}").push("latest")
                    }
                }
            }
        }
    }
}
