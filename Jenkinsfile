pipeline {
    agent any
    
    environment {
        // Define environment variables
        JAVA_HOME = '/usr/lib/jvm/java-11-amazon-corretto.x86_64'
        DOCKER_IMAGE = 'public.ecr.aws/n5h8y2y9/java-app'
        ECR_REGISTRY = 'public.ecr.aws/n5h8y2y9'
        AWS_ACCESS_KEY_ID = 'AKIATOXWRZKLUZ7VLFES'
        AWS_SECRET_ACCESS_KEY = '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'
        AWS_DEFAULT_REGION = 'us-east-1'
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
                    env.AWS_ECR_PASSWORD = sh(script: "aws ecr-public get-login-password --region us-east-1", returnStdout: true).trim()
                    sh "docker login --username AWS --password ${env.AWS_ECR_PASSWORD} ${ECR_REGISTRY}"
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    env.AWS_ECR_PASSWORD = sh(script: "aws ecr-public get-login-password --region us-east-1", returnStdout: true).trim()
                    sh "echo ${env.AWS_ECR_PASSWORD} | docker login --username AWS --password-stdin ${ECR_REGISTRY}"
                    }
                }
            }
        }
    }
}
