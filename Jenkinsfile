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
        sh '''
        python3 -m venv venv
        . venv/bin/activate
        pip install -r requirements.txt
        pip install pytest
        '''
    }
}

 stage('Test') {
    steps {
        sh '''
        . venv/bin/activate
        pytest
        '''
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
