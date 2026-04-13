pipeline {
    agent any
    stages {
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t myapp .'
            }
        }
        stage('Stop Old Container') {
            steps {
                sh 'docker stop my-running-app || true'
                sh 'docker rm my-running-app || true'
            }
        }
        stage('Run Container') {
            steps {
                sh 'docker run -d -p 3000:3000 --name my-running-app myapp'
            }
        }
    }
}

