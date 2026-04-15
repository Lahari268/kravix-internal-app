pipeline {
    agent any

    environment {
        IMAGE_NAME = "laharikalva/my_app"
    }

    stages {

        stage('Build Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }

        stage('Push Image') {
    steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
            sh '''
            docker login -u $USER -p $PASS
            docker push $IMAGE_NAME:$BUILD_NUMBER
            '''
        }
    }
}

        stage('Deploy to Dev') {
            steps {
                sh '''
                ssh ec2-user@13.61.26.69 << EOF
                docker pull $IMAGE_NAME:$BUILD_NUMBER
                docker run -d -p 3001:3000 --name devapp $IMAGE_NAME:$BUILD_NUMBER
EOF
                '''
            }
        }

        stage('Approval for QA') {
            steps {
                input message: "Deploy to QA?"
            }
        }

        stage('Deploy to QA') {
            steps {
                sh '''
                ssh ec2-user@13.60.64.164 << EOF
                docker pull $IMAGE_NAME:$BUILD_NUMBER
                docker run -d -p 3002:3000 --name qaapp $IMAGE_NAME:$BUILD_NUMBER
EOF
                '''
            }
        }

        stage('Approval for Prod') {
            steps {
                input message: "Deploy to Production?"
            }
        }

        stage('Deploy to Prod') {
            steps {
                sh '''
                ssh ec2-user@13.60.82.120 << EOF
                docker pull $IMAGE_NAME:$BUILD_NUMBER
                docker run -d -p 80:3000 --restart always --name prodapp $IMAGE_NAME:$BUILD_NUMBER
EOF
                '''
            }
        }
    }
}
