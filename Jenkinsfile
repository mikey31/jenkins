pipeline {
    agent any

    environment {
        GIT_CREDENTIALS = '2c5fac76-196e-4e0a-81b2-ae175b03fea1'    // Replace with Jenkins credentials ID for GitHub
        REPO_URL = 'https://github.com/mikey31/jenkins.git'
        TEMPLATE_FILE = 'template.xml'               // Path to the XML template file in your repository
        CSV_FILE = 'input.csv'                       // Path to the CSV file in your repository
        XML_OUTPUT_DIR = 'output'                    // Directory for storing generated XML files
    }

    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'feature/new-branch', description: 'Branch name for the new XML files')
    }

    stages {
        stage('Checkout') {
            steps {
                // Clone the repository
                git branch: 'master', credentialsId: "${GIT_CREDENTIALS}", url: "${REPO_URL}"
            }
        }

        stage('Prepare Environment') {
            steps {
                // Ensure the output directory exists
                sh "mkdir -p ${XML_OUTPUT_DIR}"
            }
        }

        stage('Generate XML Files') {
            steps {
                script {
                    // Create a Python script that will read from the CSV file and generate XML files
                    writeFile file: 'generate_xml.py', text: """
import csv
import os

# Paths
csv_path = "${CSV_FILE}"
template_path = "${TEMPLATE_FILE}"
output_dir = "${XML_OUTPUT_DIR}"

# Load CSV data
with open(csv_path, newline='') as csvfile:
    reader = csv.DictReader(csvfile)
    
    for row in reader:
        # Read the XML template and replace placeholders
        with open(template_path, 'r') as file:
            template = file.read()
            
        # Replace placeholders from CSV columns
        xml_content = template.format(
            displayName=row['name'],
            name=row['name'],
            sysDescriptions_en_US=row['sysDescriptions_en_US'],
            owner=row['owner'],
            attribute_entries='\\n'.join([f'<entry key="{k}" value="{v}"/>' for k, v in row.items() if k.startswith("test_attribute")]),
            requirements_entries='\\n'.join([f'<Reference class="sailpoint.object.Bundle" name="{req.strip()}"/>' for req in row['requirements'].split(",")])
        )
        
        # Save the XML file
        output_file = os.path.join(output_dir, f"{row['name'].replace(' ', '_')}.xml")
        with open(output_file, 'w') as out_file:
            out_file.write(xml_content)
            print(f"Generated: {output_file}")
                    """
                    
                    // Run the Python script to generate XML files
                    sh 'python3 generate_xml.py'
                }
            }
        }

        stage('Commit and Push Changes') {
            steps {
                script {
                    // Configure Git settings
                    sh 'git config user.name "Jenkins Bot"'
                    sh 'git config user.email "jenkins@example.com"'

                    // Create new branch
                    sh "git checkout -b ${params.BRANCH_NAME}"
                    
                    // Add and commit new XML files
                    sh "git add ${XML_OUTPUT_DIR}/*.xml"
                    sh 'git commit -m "Add generated XML files based on CSV input"'

                    // Push changes to the new branch
                    withCredentials([usernamePassword(credentialsId: "${GIT_CREDENTIALS}", passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh 'git push https://${GIT_USERNAME}:${GIT_PASSWORD}@${REPO_URL.replace("https://", "")} ${params.BRANCH_NAME}'
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
