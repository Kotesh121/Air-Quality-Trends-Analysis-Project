pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Sync Deployment Files') {
            steps {
                sh '''
                    cd /opt/air-quality/Air-Quality-Trends-Analysis-Project

                    git fetch origin main
                    git reset --hard origin/main

                    echo "Deployment directory updated"
                    git log -1 --oneline
                '''
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                    cd /opt/air-quality/Air-Quality-Trends-Analysis-Project

                    docker compose build
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    cd /opt/air-quality/Air-Quality-Trends-Analysis-Project

                    docker compose up -d
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    cd /opt/air-quality/Air-Quality-Trends-Analysis-Project

                    docker compose ps

                    echo "Checking backend..."
                    curl -f http://localhost:8000/healthz

                    echo ""
                    echo "Checking frontend..."
                    curl -f http://localhost
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }

        failure {
            echo 'Deployment failed!'
        }
    }
}
