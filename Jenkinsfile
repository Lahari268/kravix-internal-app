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
        // Use the ID you created in Jenkins (e.g., 'ec2-dev-key')
        withCredentials([sshUserPrivateKey(credentialsId: 'ec2-dev-key', keyFileVariable: 'SSH_KEY')]) {
            sh """
                ssh -o StrictHostKeyChecking=no -i ${SSH_KEY} ec2-user@13.61.26.69 '
                    # Stop and remove the old container if it exists
                    docker stop my-app || true
                    docker rm my-app || true
                    
                    # Pull the latest image
                    docker pull laharikalva/my_app:31
                    
                    # Run the new container
                    docker run -d --name my-app -p 3000:3000 laharikalva/my_app:31
                '
            """
        }
    }
}



        stage('Approval for QA') {
            steps {
                input message: "Deploy to QA?"
            }
        }

        stage('Deploy to QA') {
    steps {
        // Use the same credential ID if it's the same key, or create a new one for QA
        withCredentials([sshUserPrivateKey(credentialsId: 'ec2-dev-key', keyFileVariable: 'SSH_KEY')]) {
            sh """
                ssh -o StrictHostKeyChecking=no -i ${SSH_KEY} ec2-user@13.60.64.164 "
                    docker stop my-app-qa || true
                    docker rm my-app-qa || true
                    docker pull laharikalva/my_app:32
                    docker run -d --name my-app-qa -p 3000:3000 laharikalva/my_app:32
                "
            """
        }
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
