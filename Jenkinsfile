
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
                echo 'Starting Flask application...'
                bat '''
                    @echo off
                    powershell -NoProfile -Command "Start-Process -FilePath '%PYTHON_PATH%' -ArgumentList 'app.py' -WorkingDirectory '%CD%' -WindowStyle Hidden"
                '''
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
