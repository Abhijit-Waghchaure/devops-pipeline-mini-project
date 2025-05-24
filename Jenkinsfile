pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps { git 'https://github.com/you/devops-pipeline-mini-project.git' }
    }
    stage('Build Docker Image') {
      steps {
        script {
          docker.build('flask-app:latest', './app')
        }
      }
    }
    stage('Push & Deploy') {
      steps {
        sshagent(['ec2-ssh-credentials']) {
          sh '''
            docker login -u $DOCKERHUB_USER -p $DOCKERHUB_TOKEN
            docker tag flask-app:latest $DOCKERHUB_USER/flask-app:latest
            docker push $DOCKERHUB_USER/flask-app:latest
            ssh ec2-user@${EC2_HOST} "
              docker pull $DOCKERHUB_USER/flask-app:latest
              docker stop flask-app || true
              docker rm flask-app || true
              docker run -d --name flask-app -p 80:5000 $DOCKERHUB_USER/flask-app:latest
            "
          '''
        }
      }
    }
  }
}
