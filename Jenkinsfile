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

            echo "Checking containers..."
            docker compose ps

            echo "Waiting for backend health..."

            for i in {1..12}; do
                if curl -fsS http://localhost:8000/healthz; then
                    echo ""
                    echo "Backend health check passed!"
                    break
                fi

                if [ "$i" -eq 12 ]; then
                    echo "Backend failed health check!"
                    docker compose logs --tail=100 backend
                    exit 1
                fi

                echo "Backend not ready - attempt $i/12"
                sleep 5
            done

            echo "Checking frontend..."
            curl -fsS http://localhost:80 > /dev/null

            echo "Frontend health check passed!"

            echo "Final container status:"
            docker compose ps

            echo "Deployment verification successful!"
        '''
    }
}
       }
