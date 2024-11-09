pipeline {
    agent any

    parameters {
        file(name: 'CSV_FILE', description: 'Upload a CSV file to process')
    }

    stages {
        stage('Check Uploaded CSV') {
            steps {
                script {
                    // Check if the file is uploaded
                    if (!params.CSV_FILE) {
                        error "CSV file not uploaded."
                    } else {
                        echo "CSV file uploaded: ${params.CSV_FILE}"
                    }
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Ensure Python and pandas are available to handle CSV reading
                    bat '''
                    python --version || echo Please install Python manually.
                    pip install pandas
                    '''
                }
            }
        }

        stage('Process CSV File') {
            steps {
                script {
                    // Write a Python script to read the CSV file
                    writeFile file: 'process_csv.py', text: """
import pandas as pd

# Read the CSV file and display its contents
df = pd.read_csv('${params.CSV_FILE}')
print("CSV Content:")
print(df)
                    """

                    // Run the Python script using the bat command
                    bat 'python process_csv.py'
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed!'
            deleteDir()  // Clean up workspace
        }
    }
}
