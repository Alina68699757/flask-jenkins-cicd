
pipeline {
    agent any

    environment {
        PYTHON_PATH = 'D:\\develop\\python.exe'
        APP_PORT = '5000'
    }

    stages {
        stage('Verify Python') {
            steps {
                bat '"%PYTHON_PATH%" --version'
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                bat '"%PYTHON_PATH%" -m pip install -r requirements.txt'
            }
        }

        stage('Lint') {
            steps {
                bat '"%PYTHON_PATH%" -m flake8 app.py tests'
            }
        }

        stage('Test') {
            steps {
                bat '''
                    "%PYTHON_PATH%" -m pytest tests -v --cov=app --cov-report=html:htmlcov --cov-report=term
                '''
            }
            post {
                always {
                    publishHTML(target: [
                        reportDir: 'htmlcov',
                        reportFiles: 'index.html',
                        reportName: 'Coverage Report',
                        keepAll: true,
                        allowMissing: true,
                        alwaysLinkToLastBuild: true
                    ])
                }
            }
        }

        stage('Build') {
            steps {
                bat '''
                    "%PYTHON_PATH%" -m pip install pyinstaller
                    "%PYTHON_PATH%" -m PyInstaller --onefile --name flask-web-app app.py
                '''
                archiveArtifacts artifacts: 'dist/*.exe', fingerprint: true
            }
        }

        
stage('Deploy') {
    steps {
        echo 'Deploying Flask application...'

        bat '''
            @echo off
            if not exist "D:\\develop\\flask-jenkins-cicd" (
                mkdir "D:\\develop\\flask-jenkins-cicd"
            )
            copy /Y app.py "D:\\develop\\flask-jenkins-cicd\\app.py"
        '''

        echo 'Application files deployed successfully.'
    }
}

    }

    post {
        success {
            echo 'CI/CD pipeline completed successfully!'
        }
        failure {
            echo 'CI/CD pipeline failed!'
        }
    }
}
