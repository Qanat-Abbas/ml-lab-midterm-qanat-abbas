pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/Qanat-Abbas/ml-lab-midterm-qanat-abbas.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                python3 -m pip install --upgrade pip
                pip3 install -r requirements.txt
                '''
            }
        }

        stage('Fetch Dataset') {
            steps {
                sh '''
                echo "Dataset already in repo or generated"
                ls -la dataset
                '''
            }
        }

        stage('Train Model') {
            steps {
                sh '''
                echo "Training model..."
                python3 train.py
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
                echo "Stopping old container..."
                docker stop ml-container || true
                docker rm ml-container || true

                echo "Running new container..."
                docker run -d -p 8000:8000 --name ml-container ml-api
                '''
            }
        }
    }
}
