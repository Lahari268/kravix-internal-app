pipeline {
    agent any

    stages {

        stage('Clone') {
            steps {
                git 'https://github.com/Lahari268/kravix-internal-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t myapp .'
            }
        }

        stage('Stop Old Container') {
            steps {
                sh 'docker stop mycontainer || true'
                sh 'docker rm mycontainer || true'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run -d -p 3000:3000 --name mycontainer myapp'
            }
        }
    }
}
