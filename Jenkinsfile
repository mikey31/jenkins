pipeline {
    agent {
        docker {
            image 'python:3.8'    // Specifies the Docker image with Python3 preinstalled
            args '-u root'        // Runs as root to allow installing dependencies if needed
        }
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout your code from Git
                git credentialsId: 'github-credentials-id', url: 'https://github.com/mikey31/jenkins.git'
            }
        }

        stage('Generate XML') {
            steps {
                // Run the Python script
                sh 'python3 generate_xml.py'
            }
        }
    }
}
