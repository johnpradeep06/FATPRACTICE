pipeline {
    agent any

    environment {
        // Your DockerHub credentials ID
        DOCKERHUB_CREDENTIALS = credentials('johnpradeep06')
        IMAGE_NAME = 'johnpradeep06/labfat-app'
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Checkout') {
            steps {
                // Uses the repository configured in the Jenkins Job GUI automatically
                checkout scm
            }
        }

        stage('Maven Build') {
            steps {
                echo "Packaging the Spring Boot application..."
                // WE MUST CREATE THE JAR FILE BEFORE DOCKER BUILD!
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('Build Image') {
            steps {
                script {
                    echo "Building Docker image ${IMAGE_NAME}:${IMAGE_TAG}"
                    bat "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Push Image') {
            steps {
                echo "Logging into DockerHub..."
                bat """
                docker login -u %DOCKERHUB_CREDENTIALS_USR% -p %DOCKERHUB_CREDENTIALS_PSW%
                """

                echo "Pushing image to DockerHub..."
                bat "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    echo "Deploying to Kubernetes..."

                    // Explicitly set kubeconfig path and apply the single deployment file
                    bat '''
                    set KUBECONFIG=C:\\ProgramData\\Jenkins\\.jenkins\\.kube\\config
                    kubectl apply -f deployment.yaml
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for details.'
        }
    }
}
