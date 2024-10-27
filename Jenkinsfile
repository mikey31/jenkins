pipeline {
    agent any

    environment {
        GIT_CREDENTIALS = 'github-credentials-id'
        REPO_URL = 'https://github.com/mikey31/jenkins.git'
        TEMPLATE_FILE = 'template.xml'
        CSV_FILE = 'input.csv'
        XML_OUTPUT_DIR = 'output'
    }

    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'feature/new-branch', description: 'Branch name for the new XML files')
    }

    stages {
        stage('Run in Docker') {
            steps {
                script {
                    sh '''
                    docker run --rm -v $(pwd):/workspace -w /workspace python:3.8 /bin/bash -c "
                        pip install -r requirements.txt || true;  # Install any Python dependencies if needed
                        python3 generate_xml.py
                    "
                    '''
                }
            }
        }

        stage('Commit and Push Changes') {
            steps {
                script {
                    sh 'git config user.name "Jenkins Bot"'
                    sh 'git config user.email "jenkins@example.com"'
                    sh "git checkout -b ${params.BRANCH_NAME}"
                    sh "git add ${XML_OUTPUT_DIR}/*.xml"
                    sh 'git commit -m "Add generated XML files based on CSV input"'

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
