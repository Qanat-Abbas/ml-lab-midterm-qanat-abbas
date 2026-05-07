pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/Qanat-Abbas/ml-lab-midterm-qanat-abbas.git'
            }
        }

        stage('Create Virtual Environment') {
            steps {
                sh '''
                python3 -m venv venv
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                . venv/bin/activate

                python -m pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Fetch Dataset') {
            steps {
                sh '''
                echo "Checking dataset..."
                ls -la dataset
                '''
            }
        }

        stage('Train Model') {
            steps {
                sh '''
                . venv/bin/activate

                echo "Training model..."
                python train.py
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                echo "Building Docker image..."
                docker build -t ml-api .
                '''
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                echo "Stopping old container if exists..."
                docker stop ml-container || true
                docker rm ml-container || true

                echo "Running new container..."
                docker run -d -p 8000:8000 --name ml-container ml-api
                '''
            }
        }
    }
}
