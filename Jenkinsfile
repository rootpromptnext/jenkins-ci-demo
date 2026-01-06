pipeline {
  agent any

  environment {
    IMAGE_NAME = 'jenkins-ci'
    IMAGE_TAG = 'v1'
    DOCKER_USER = 'rootpromptnext'
  }

  stages {
    stage('Clone repo') {
      steps {
        sh 'rm -fr jenkins-ci-demo'
        sh 'echo "Cloning a repo..."'
        sh 'git clone https://github.com/prayag-sangode/jenkins-ci-demo.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh '''
          echo "--- Checking Docker Access ---"
          id
          whoami
          groups
          ls -l /var/run/docker.sock

          echo "--- Building the Docker image ---"
          cd jenkins-ci-demo
          docker build -t ${IMAGE_NAME}:${IMAGE_TAG} -f Dockerfile .
        '''
      }
    }

    stage('Docker Login & Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
          sh '''
            echo "--- Logging into Docker Hub ---"
            echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin

            echo "--- Tagging and Pushing Image ---"
            docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}
            docker push ${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}
          '''
        }
      }
    }
  }

  post {
    success {
      echo "Docker image ${DOCKER_USER}/${IMAGE_NAME}:${IMAGE_TAG} pushed successfully!"
    }
    failure {
      echo "Build or push failed. Check logs above."
    }
  }
}
