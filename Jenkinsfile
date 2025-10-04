pipeline {
  agent any

  triggers {
    pollSCM('H/2 * * * *')
  }

  environment {
    APP_NAME = 'nginx-local-app'
    APP_PORT = '8081'
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build Docker Image') {
      steps {
        echo 'Building Docker image...'
        sh "docker build -t ${APP_NAME} ."
      }
    }

    stage('Stop Old Container') {
      steps {
        echo 'Stopping old container if exists...'
        sh "docker rm -f ${APP_NAME} || true"
      }
    }

    stage('Run New Container') {
      steps {
        echo 'Running new container...'
        sh "docker run -d --name ${APP_NAME} -p ${APP_PORT}:80 ${APP_NAME}"
      }
    }

    stage('Test (health-check)') {
    steps {
        echo "Health check on http://host.docker.internal:${APP_PORT}"
        sh '''
        for i in $(seq 1 15); do
            code=$(curl -s -o /dev/null -w "%{http_code}" http://host.docker.internal:$APP_PORT || true)
            if [ "$code" = "200" ]; then
            echo "OK (HTTP 200)";
            exit 0
            fi
            echo "Waiting for app... ($i)"; sleep 1
        done
        echo "Health-check failed"
        exit 1
        '''
    }
    }
  }

  post {
    success { echo 'Deployment successful!' }
    failure {
      echo 'Deployment failed! Showing logs (if any)...'
      sh "docker logs ${APP_NAME} || true"
    }
  }
}
