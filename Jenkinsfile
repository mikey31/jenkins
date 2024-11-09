pipeline {
    agent any

    parameters {
        file(name: 'CSV_FILE', description: 'Upload a CSV file to process')
    }

    environment {
        CSV_FILENAME = "uploaded_file.csv"
    }

    stages {
        stage('Save Uploaded CSV') {
            steps {
                script {
                    // Rename the uploaded file to a consistent filename
                    if (fileExists(params.CSV_FILE)) {
                        sh "mv ${params.CSV_FILE} ${env.CSV_FILENAME}"
                    } else {
                        error "CSV file not uploaded."
                    }
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Install Python and pandas if they are not already available
                    sh 'which python3 || sudo apt-get install -y python3'
                    sh 'pip3 install pandas'
                }
            }
        }

        stage('Process CSV File') {
            steps {
                script {
                    // Write and execute a Python script to read the CSV
                    writeFile file: 'process_csv.py', text: '''
import pandas as pd

# Read the CSV file and print its content
df = pd.read_csv("uploaded_file.csv")
print("CSV Content:")
print(df)
                    '''

                    // Run the Python script
                    sh 'python3 process_csv.py'
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed!'
            deleteDir()  // Clean up the workspace
        }
    }
}
