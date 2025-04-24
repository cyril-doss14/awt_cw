pipeline {
  agent any

  environment {
    DOCKER_TAG = 'latest'
  }

  stages {
    stage('Build Docker Image') {
      steps {
        dir('Startup-app') {
          sh 'docker-compose build'
        }
      }
    }

    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: "${DOCKER_REGISTRY_CREDS}", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
          sh '''
            echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
            docker tag startup-app_app:latest $DOCKER_IMAGE:$DOCKER_TAG
            docker push $DOCKER_IMAGE:$DOCKER_TAG
          '''
        }
      }
    }

    stage('Deploy Updated App') {
      steps {
        dir('Startup-app') {
          sh '''
            docker-compose down
            docker-compose pull
            docker-compose up -d
          '''
        }
      }
    }
  }

  post {
    always {
      echo '✔️ Pipeline completed.'
    }
    failure {
      mail to: 'cyrilrodriguez007@gmail.com',
           subject: '❌ Jenkins Build Failed',
           body: 'Check your Jenkins console output for failure details.'
    }
  }
}
