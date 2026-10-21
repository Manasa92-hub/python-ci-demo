pipeline {
    agent any

    triggers {
        // Check GitHub every 2 minutes and build if there is a new commit
        pollSCM('H/2 * * * *')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo 'Source code checked out'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Lint') {
            steps {
                sh '''
                    . venv/bin/activate
                    flake8 . --exclude venv --max-line-length=100
                '''
            }
        }

        stage('Unit Tests') {
            steps {
                sh '''
                    . venv/bin/activate
                    pytest --junitxml=report.xml --html=report.html --self-contained-html
                '''
            }
        }
    }

    post {
        always {
            junit allowEmptyResults: true, testResults: 'report.xml'
            archiveArtifacts artifacts: 'report.html', allowEmptyArchive: true
        }
        success {
            echo 'BUILD PASSED: all checks are green'
        }
        failure {
            echo 'BUILD FAILED: check the lint or test stage'
        }
    }
}
