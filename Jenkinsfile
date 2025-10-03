pipeline {
    agent any   // run on any available agent

    stages {
        stage('Checkout') {
            steps {
                // Pull code from GitHub repo
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t nginx-local-app .'
            }
        }

        stage('Stop Old Container') {
            steps {
                echo 'Stopping old container if exists...'
                sh 'docker rm -f nginx-local-app || true'
            }
        }

        stage('Run New Container') {
            steps {
                echo 'Running new container...'
                sh 'docker run -d --name nginx-local-app -p 8080:80 nginx-local-app'
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
