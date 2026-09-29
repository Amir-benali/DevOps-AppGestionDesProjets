pipeline {
    agent any

    environment {
        BACKEND_PORT = '18082'
        FRONTEND_PORT = '18083'
    }

    stages {
        stage('Build Docker Images') {
            steps {
                sh 'docker compose build --pull'
            }
        }

        stage('Start Application') {
            steps {
                sh 'docker compose up -d'
            }
        }

        stage('Smoke Test') {
            steps {
                sh '''
                    for attempt in $(seq 1 60); do
                        if curl --fail --silent http://localhost:8083/ > /dev/null \\
                            && curl --fail --silent http://localhost:18082/entreprise/all > /dev/null; then
                            echo "Frontend and backend are responding."
                            exit 0
                        fi

                        echo "Waiting for the application to start (attempt ${attempt}/60)..."
                        sleep 2
                    done

                    docker compose ps
                    docker compose logs --no-color
                    exit 1
                '''
            }
        }
    }

    post {
        always {
            sh 'docker compose down --volumes --remove-orphans'
        }
    }
}