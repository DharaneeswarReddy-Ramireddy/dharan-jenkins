pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from GitHub
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Build the Docker image
                sh 'docker build -t flask-app .'
            }
        }

        stage('Test') {
            steps {
                // Stop and remove any existing container named flask-test
                sh 'docker stop flask-test || true'
                sh 'docker rm flask-test || true'

                // Stop any process using port 5000
                sh "fuser -k 5000/tcp || true"

                // Run the Docker container and check if it starts successfully
                sh 'docker run -d --name flask-test -p 5000:5000 flask-app'
                sh 'sleep 5' // Wait for the container to start

                // Test if the Flask app is responding
                sh 'curl -f http://localhost:5000 || exit 1'

                // Clean up the test container
                sh 'docker stop flask-test'
                sh 'docker rm flask-test'
            }
        }

        stage('Deploy') {
            steps {
                // Deploy the container (running the Flask app)
                sh 'docker run -d -p 5000:5000 flask-app'
            }
        }
    }
}
