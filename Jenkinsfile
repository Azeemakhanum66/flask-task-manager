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
                bat """
                   docker build -t ${IMAGE_NAME}:latest .
                """
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    bat """
                       echo %PASS% | docker login -u %USER% --password-stdin
                       docker tag ${IMAGE_NAME}:latest ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest
                       docker push ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat """
                   kubectl delete deployment task-manager-deployment || exit 0
                   kubectl apply -f deployment.yaml
                """
            }
        }
    }
}
