pipeline {
    agent any

    environment {
        IMAGE_NAME = "my-python-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Cloning source code...'
                git branch: 'main',
                    url: 'https://github.com/Abdallah9858/app'
            }
        }

        stage('Build') {
            steps {
                echo 'Installing dependencies...'
                sh 'pip install --break-system-packages -r requirements.txt'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'python -m pytest tests/'
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker image...'
                sh """
                docker build \
                -t ${IMAGE_NAME}:${IMAGE_TAG} .
                """
            }
        }

        stage('Push Image') {
            steps {
                echo 'Pushing image...'

                sh """
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} \
                uat-registry.cwallet.qa/${IMAGE_NAME}:${IMAGE_TAG}

                docker push \
                uat-registry.cwallet.qa/${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'

                sh """
                kubectl set image deployment/python-app \
                python-app=uat-registry.cwallet.qa/${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }
    }

    post {
        success {
            echo 'Deployment successful'
        }

        failure {
            echo 'Pipeline failed'
        }
    }
}
