pipeline {
    agent any

    environment {
        IMAGE_NAME = "laharikalva/my_app"
    }

    stages {

        stage('Build Image') {
            steps {
                sh 'docker build -t laharikalva/my_app:$BUILD_NUMBER .'
            }
        }

        stage('Push Image') {
    steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
            sh '''
            docker login -u $USER -p $PASS
            docker push laharikalva/my_app:$BUILD_NUMBER
            '''
        }
    }
}

       stage('Deploy to Dev') {
    steps {
        withCredentials([sshUserPrivateKey(credentialsId: 'ec2-dev-key', keyFileVariable: 'SSH_KEY')]) {
            sh """
                ssh -o StrictHostKeyChecking=no -i ${SSH_KEY} ec2-user@13.61.26.69 "
                    # Stop/Remove old container
                    docker stop my-app || true
                    docker rm my-app || true
                    
                    # Pull the SPECIFIC version we just built
                    docker pull laharikalva/my_app:${env.BUILD_NUMBER}
                    
                    # Run the NEW version
                    docker run -d --name my-app -p 3000:3000 laharikalva/my_app:${env.BUILD_NUMBER}
                "
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
        // Use the same credential ID you used for Dev/QA
        withCredentials([sshUserPrivateKey(credentialsId: 'ec2-dev-key', keyFileVariable: 'SSH_KEY')]) {
            sh """
                ssh -o StrictHostKeyChecking=no -i ${SSH_KEY} ec2-user@13.60.82.120 "
                    docker stop my-app-prod || true
                    docker rm my-app-prod || true
                    
                    # Use the build number variable here!
                    docker pull laharikalva/my_app:${env.BUILD_NUMBER}
                    docker run -d --name my-app-prod -p 3000:3000 laharikalva/my_app:${env.BUILD_NUMBER}
                "
            """
        }
    }
}
    }

}
