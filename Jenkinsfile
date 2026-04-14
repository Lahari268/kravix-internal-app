pipeline {
    agent any

    environment {
        IMAGE_NAME = "laharikalva-my_app"
        EC2_USER = "ubuntu"
        EC2_IP = "13.61.13.243"
    }

    stages {

        stage('Clone Code') {
            steps {
                git 'https://github.com/Lahari268/kravix-internal-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t laharikalva-my_app:latest .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh 'docker push laharikalva-my_app:latest'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh '''
                ssh -o StrictHostKeyChecking=no $EC2_USER@$EC2_IP << EOF
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
