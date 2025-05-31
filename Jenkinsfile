pipeline {
    agent any

    environment {
        IMAGE_NAME = "task-manager-app"
        DOCKERHUB_USERNAME = "azeemakhanum"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/Azeemakhanum66/flask-task-manager.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                   docker build -t ${IMAGE_NAME}:latest .
                """
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh """
                       echo $PASS | docker login -u $USER --password-stdin
                       docker tag ${IMAGE_NAME}:latest ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest
                       docker push ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                   kubectl delete deployment task-manager-deployment || true
                   kubectl apply -f deployment.yaml
                """
            }
        }
    }
}
