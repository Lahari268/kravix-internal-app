pipeline {
    agent any

    environment {
        IMAGE_NAME = "laharikalva/my_app"
        EC2_IP = "13.61.13.243"
        EC2_USER = "ubuntu"
    }

    stages {

        stage('Clone') {
            steps {
                git 'https://github.com/Lahari268/kravix-internal-app.git'
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                    echo "$PASS" | docker login -u "$USER" --password-stdin
                    docker push $IMAGE_NAME:latest
                    '''
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no $EC2_USER@$EC2_IP "
                    docker pull $IMAGE_NAME:latest &&
                    docker stop myapp || true &&
                    docker rm myapp || true &&
                    docker run -d -p 80:3000 --name myapp $IMAGE_NAME:latest
                    "
                    '''
                }
            }
        }
    }
}
