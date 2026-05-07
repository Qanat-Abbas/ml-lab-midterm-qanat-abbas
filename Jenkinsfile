pipeline {
    agent any

    environment {
        IMAGE_NAME = "ml-api"
        CONTAINER_NAME = "ml-container"
        PORT = "8000"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                sh '''
                echo "Cleaning old workspace artifacts..."
                rm -rf venv
                rm -rf __pycache__
                rm -rf *.pkl
                '''
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/Qanat-Abbas/ml-lab-midterm-qanat-abbas.git'
            }
        }

        stage('Verify Dataset') {
            steps {
                sh '''
                echo "Checking dataset..."
                ls -la dataset

                echo "Dataset row count:"
                wc -l dataset/train.csv
                '''
            }
        }

        stage('Train Model Inside Docker') {
            steps {
                sh '''
                echo "Training model inside Docker container..."

                docker run --rm \
                -v $(pwd):/app \
                -w /app \
                python:3.10 bash -c "
                    pip install --no-cache-dir -r requirements.txt &&
                    python train.py
                "
                '''
            }
        }

        stage('Verify Model Files') {
            steps {
                sh '''
                echo "Checking generated model artifacts..."

                ls -la

                test -f model.pkl
                test -f metrics.json

                echo "Metrics file content:"
                cat metrics.json
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                echo "Removing old Docker image..."
                docker rmi -f $IMAGE_NAME || true

                echo "Building fresh Docker image..."
                docker build --no-cache -t $IMAGE_NAME .
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

                echo "Freeing occupied port..."
                fuser -k ${PORT}/tcp || true
                '''
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                echo "Starting new container..."

                docker run -d \
                -p ${PORT}:8000 \
                --name $CONTAINER_NAME \
                $IMAGE_NAME
                '''
            }
        }

        stage('Verify API') {
            steps {
                sh '''
                echo "Waiting for API startup..."
                sleep 10

                echo "Testing API endpoint..."
                curl -f http://localhost:${PORT}/metrics

                echo ""
                echo "API verification successful ✅"
                '''
            }
        }
    }

    post {

        success {
            echo "=================================="
            echo "PIPELINE SUCCESS 🚀"
            echo "Model retrained successfully"
            echo "Docker container deployed"
            echo "API is live on port ${PORT}"
            echo "=================================="
        }

        failure {
            echo "=================================="
            echo "PIPELINE FAILED ❌"
            echo "Check Jenkins console logs"
            echo "=================================="
        }

        always {
            sh '''
            echo "Running containers:"
            docker ps -a || true
            '''
        }
    }
}
