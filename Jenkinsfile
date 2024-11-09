pipeline {
    agent any

    environment {
        GIT_CREDENTIALS = '2c5fac76-196e-4e0a-81b2-ae175b03fea1'
        REPO_URL = 'https://github.com/mikey31/jenkins.git'
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    checkout([$class: 'GitSCM',
                        branches: [[name: '*/main']],
                        userRemoteConfigs: [[url: env.REPO_URL, credentialsId: env.GIT_CREDENTIALS]]
                    ])
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Install Python if not available; adjust as needed for other dependencies.
                    sh 'which python3 || sudo apt-get install -y python3'
                    sh 'pip3 install pandas'
                }
            }
        }

        stage('Read CSV File') {
            steps {
                script {
                    // Add a Python script to read and process the CSV.
                    writeFile file: 'process_csv.py', text: '''
import pandas as pd

# Adjust 'file.csv' to the actual path within your repository
df = pd.read_csv('file.csv')
print("CSV Content:")
print(df)
'''

                    // Execute the Python script
                    sh 'python3 process_csv.py'
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed!'
        }
    }
}
