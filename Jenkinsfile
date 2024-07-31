pipeline {
    agent any

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
                git branch: 'main' url: ''
            }

        }

        stage(image build){
            steps{
                sh 'docker build -t ice-cream'
            }
        }

    }
}