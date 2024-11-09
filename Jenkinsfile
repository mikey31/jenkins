pipeline {
    agent any

    parameters {
        file(name: 'CSV_FILE', description: 'Upload a CSV file to process')
    }

    environment {
        CSV_FILENAME = "input.csv"
    }

    stages {
        stage('Save Uploaded CSV') {
            steps {
                script {
                    // Check if the file is uploaded
                    if (!params.CSV_FILE) {
                        error "CSV file not uploaded."
                    }

                    // Save the uploaded file to a specific filename
                    writeFile file: env.CSV_FILENAME, text: params.CSV_FILE.readToString()
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Ensure Python and pandas are available to handle CSV reading
                    sh 'which python3 || sudo apt-get install -y python3'
                    sh 'pip3 install pandas'
                }
            }
        }

        stage('Process CSV File') {
            steps {
                script {
                    // Write a Python script to process the CSV file
                    writeFile file: 'process_csv.py', text: '''
import pandas as pd

# Read the CSV file and display its contents
df = pd.read_csv("input.csv")
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
            deleteDir()  // Cleanup workspace
        }
    }
}
