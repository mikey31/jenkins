pipeline {
    agent any

    parameters {
        file(name: 'CSV_FILE', description: 'Upload the CSV file for processing')
    }

    stages {
        stage('Verify Uploaded CSV File') {
            steps {
                script {
                    // Check if CSV_FILE parameter is recognized
                    if (params.CSV_FILE == null) {
                        error "CSV file not uploaded. Please upload a valid CSV file with each build."
                    } else {
                        echo "CSV file parameter found. Path: ${params.CSV_FILE}"
                    }

                    // Confirm file exists in the workspace
                    if (!fileExists("${params.CSV_FILE}")) {
                        error "CSV file not found in workspace after upload. Please check the file parameter configuration."
                    } else {
                        echo "CSV file successfully recognized and located in workspace at: ${params.CSV_FILE}"
                    }
                }
            }
        }
    }
}
