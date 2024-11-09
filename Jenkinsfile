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
                    // Check if the file is uploaded and rename it for consistent access
                    if (fileExists(params.CSV_FILE)) {
                        bat "rename ${params.CSV_FILE} ${env.CSV_FILENAME}"
                    } else {
                        error "CSV file not uploaded."
                    }
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Install Python and necessary packages if not already installed
                    // Adjust this to your specific environment setup if needed
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
                    writeFile file: 'process_csv.py', text: '''
import pandas as pd

# Read the CSV file and display its contents
df = pd.read_csv("uploaded_file.csv")
print("CSV Content:")
print(df)
                    '''

                    // Run the Python script 
