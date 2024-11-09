pipeline {
    agent any

    environment {
        GIT_CREDENTIALS = '2c5fac76-196e-4e0a-81b2-ae175b03fea1'  // Your Jenkins GitHub credentials ID
        REPO_URL = 'https://github.com/mikey31/jenkins.git'
        TEMPLATE_FILE = 'template.xml'
        XML_OUTPUT_DIR = 'output'
    }

    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'feature/new-branch', description: 'Branch name for the new XML files')
        file(name: 'CSV_FILE', description: 'Upload the CSV file to use for generating XML files')  // File parameter for CSV
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', credentialsId: "${GIT_CREDENTIALS}", url: "${REPO_URL}"
            }
        }

        stage('Prepare Environment') {
            steps {
                bat """
                if not exist ${XML_OUTPUT_DIR} (
                    mkdir ${XML_OUTPUT_DIR}
                )
                """
            }
        }

        stage('Get Uploaded CSV File Path') {
            steps {
                script {
                    // Retrieve the path of the uploaded CSV file
                    def csvFilePath = params.CSV_FILE.getAbsolutePath()
                    echo "CSV file path: ${csvFilePath}"
                    
                    // Set the path as an environment variable for later use
                    env.UPLOADED_CSV_FILE = csvFilePath
                }
            }
        }

        stage('Generate XML Files') {
            steps {
                script {
                    // Write the Python script that generates XML files
                    writeFile file: 'generate_xml.py', text: """
import csv
import os
import sys

# Use the CSV file passed as an argument
csv_path = sys.argv[1]
template_path = "${TEMPLATE_FILE}"
output_dir = "${XML_OUTPUT_DIR}"

with open(csv_path, newline='') as csvfile:
    reader = csv.DictReader(csvfile)
    
    for row in reader:
        with open(template_path, 'r') as file:
            template = file.read()
            
        xml_content = template.format(
            displayName=row['name'],
            name=row['name'],
            sysDescriptions_en_US=row['sysDescriptions_en_US'],
            owner=row['owner'],
            attribute_entries='\\n'.join(['<entry key="{0}" value="{1}"/>'.format(k, v) for k, v in row.items() if k.startswith("test_attribute")]),
            requirements_entries='\\n'.join(['<Reference class="sailpoint.object.Bundle" name="{0}"/>'.format(req.strip()) for req in row['requirements'].split(",")])
        )
        
        output_file = os.path.join(output_dir, "{}.xml".format(row['name'].replace(' ', '_')))
        with open(output_file, 'w') as out_file:
            out_file.write(xml_content)
            print("Generated: {}".format(output_file))
                    """
                    
                    // Run the Python script with the exact path to the uploaded CSV file
                    bat """
                    set PATH=C:\\Users\\Mick\\AppData\\Local\\Programs\\Python\\Python39;%PATH%
                    python generate_xml.py "%UPLOADED_CSV_FILE%"
                    """
                }
            }
        }

        stage('Commit and Push Changes') {
            steps {
                script {
                    bat 'git config user.name "Jenkins Bot"'
                    bat 'git config user.email "jenkins@example.com"'
                    bat "git checkout -b ${params.BRANCH_NAME}"
                    bat "git add ${XML_OUTPUT_DIR}\\*.xml"
                    bat 'git commit -m "Add generated XML files based on CSV input"'

                    withCredentials([usernamePassword(credentialsId: "${GIT_CREDENTIALS}", passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        bat 'git push https://${GIT_USERNAME}:${GIT_PASSWORD}@${REPO_URL.replace("https://", "")} ${params.BRANCH_NAME}'
                    }
                }
            }
        }
    }

    post {
        success {
            echo "XML files generated and pushed to GitHub on branch ${params.BRANCH_NAME}."
        }
        failure {
            echo "Pipeline failed. Check the logs for details."
        }
    }
}
