pipeline {
    agent any

    environment {
        IMAGE_NAME = "python-devops-app"
        DOCKER_HUB = "your-dockerhub-username"
        BUILD_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Setup Python Environment') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                pip install --upgrade pip
                pip install -r app/requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                . venv/bin/activate
                pytest tests/
                '''
            }
        }

        stage('Build, Tag & Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {

                    sh '''
                    echo "🔐 Docker Login"
                    echo $PASS | docker login -u $USER --password-stdin

                    echo "🐳 Building Docker Image"
                    docker build -t $DOCKER_HUB/$IMAGE_NAME:$BUILD_TAG app/

                    echo "🏷️ Tagging as latest"
                    docker tag $DOCKER_HUB/$IMAGE_NAME:$BUILD_TAG \
                               $DOCKER_HUB/$IMAGE_NAME:latest

                    echo "📤 Pushing Images"
                    docker push $DOCKER_HUB/$IMAGE_NAME:$BUILD_TAG
                    docker push $DOCKER_HUB/$IMAGE_NAME:latest
                    '''
                }
            }
        }

        stage('Deploy (Optional)') {
            steps {
                echo "🚀 Deploy to Kubernetes / EC2 here"
            }
        }
    }

    post {
        success {
            echo "🎉 Pipeline completed successfully!"
            echo "✅ Image: $DOCKER_HUB/$IMAGE_NAME:$BUILD_TAG"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
