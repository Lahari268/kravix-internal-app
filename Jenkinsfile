pipeline {
    agent any
    environment {
        IMAGE_NAME = "laharikalva/my_app"
    }
    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/Lahari628/kravix-internal-app.git'
            }
    }
    stage('Build Image') {
        steps {
            sh 'docker build -t $IMAGE_NAME:latest .'
        }
    }
    stage('Push to DockerHub') {
        steps {
            sh 'docker push $IMAGE_NAME:latest'
        }
    }
    stage('Deploy to EC2') {
        steps {
            sh '''
            ssh ubuntu@13.61.13.243 << EOF
            docker pull $IMAGE_NAME:latest
            docker stop myapp || true
            docker rm myapp || true
            docker run -d -p 80:3000 --name myapp $IMAGE_NAME:latest
            EOF
            '''

            }
        }
    }
}
