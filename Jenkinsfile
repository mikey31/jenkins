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
                    // Attempt to save the uploaded file content
                    if (!params.CSV_FILE) {
                        error "CSV file not uploaded."
                    } else {
                        // Save the file content to a specified file
                        writeFile file: env.CSV_FILENAME, text: readFile(file: params.CSV_FILE)
                        echo "CSV file successfully uploaded and saved as ${env.CSV_FILENAME}"
                    }
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Install Python and pandas if not already available
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
df = pd.read_csv('${env.CSV_FILENAME}')
print("CSV Content:")
print(df)
                    """

                    // Run the Python script
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
