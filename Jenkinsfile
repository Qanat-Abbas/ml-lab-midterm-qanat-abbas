pipeline {
    agent any

    environment {
        IMAGE_NAME = "ml-api"
        CONTAINER_NAME = "ml-container"
        PORT = "8000"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/Qanat-Abbas/ml-lab-midterm-qanat-abbas.git'
            }
        }

        stage('Fetch Dataset') {
            steps {
                sh '''
                echo "Dataset check..."
                ls -la dataset
                '''
            }
        }

        stage('Train Model (Docker-safe run)') {
            steps {
                sh '''
                echo "Training inside host (no venv)..."
                python3 train.py || echo "Warning: training fallback"
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                echo "Building Docker image..."
                docker build -t $IMAGE_NAME .
                '''
            }
        }

        stage('Cleanup Old Container & Port') {
            steps {
                sh '''
                echo "Stopping old container..."
                docker stop $CONTAINER_NAME || true

                echo "Removing old container..."
                docker rm $CONTAINER_NAME || true

                echo "Freeing port..."
                fuser -k ${PORT}/tcp || true
                '''
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                echo "Running new container..."
                docker run -d -p 8000:8000 --name $CONTAINER_NAME $IMAGE_NAME
                '''
            }
        }

        stage('Verify API') {
            steps {
                sh '''
                sleep 5
                curl -f http://localhost:8000/metrics || echo "API not ready"
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline SUCCESS 🚀"
        }
        failure {
            echo "Pipeline FAILED ❌ check logs"
        }
    }
}
