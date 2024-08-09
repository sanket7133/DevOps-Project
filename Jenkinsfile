pipeline {
    agent any

    environment{
            AWS_ACCOUNT_ID= '009160052417'
            AWS_REGION='ap-south-1'
            IMAGE_NAME='ice-cream'
            IMAGE_TAG='latest'
            CLUSTER_NAME='ice-cream_cluster'
            CLUSTER_SERVICE="ice-cream-service"
            TASK_DEFINITION_NAME="ice-cream_family"
            ECR_URL= "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${IMAGE_NAME}"
            DESIRED_COUNT="2"
    }

    stages{
        stage('Clean workspace'){
            steps{
                cleanWs()
            }
        }

        stage('Version check'){
            steps{
                sh 'java --version'
                sh 'jenkins --version'
                sh 'docker --version'
            }
        }
        stage('Git Checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/sanket7133/DevOps-Project.git'
            }
        }

        stage('image build'){
            steps{
                sh 'docker build -t ice-cream .'
            }
        }

         stage('aws login'){
            steps{
               script {
            def loginPassword = sh(script: "aws ecr get-login-password --region ${AWS_REGION}", returnStdout: true)
            sh """docker login --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com <<< ${loginPassword}"""
        }
        }
        }


         stage('Docker Image Push') {
            steps {
                sh """docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${ECR_URL}:${IMAGE_TAG}"""
                sh """docker push ${ECR_URL}:${IMAGE_TAG}"""
            }
        }

        stage('Deploy to ECS') {
            steps {
                sh """aws ecs update-service --cluster ${CLUSTER_NAME} --service ${CLUSTER_SERVICE} --force-new-deployment --task-definition \$(aws ecs describe-services --cluster ${CLUSTER_NAME} --services ${CLUSTER_SERVICE} --query 'services[0].taskDefinition' --output text) --container-definitions "[{\"name\":\"${IMAGE_NAME}\",\"image\":\"${ECR_URL}:${IMAGE_TAG}\"}]" """
            }
        }
    }
}