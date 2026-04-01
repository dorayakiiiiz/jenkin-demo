pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    python3 -m pip install --upgrade pip
                    if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
                    pip install ruff pytest coverage
                '''
            }
        }

        stage('Lint') {
            steps {
                // "continue-on-error" trong GHA tương đương với catchError trong Jenkins
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    sh 'ruff --target-version=py310 .'
                }
            }
        }

        stage('Test & Coverage') {
            steps {
                sh 'coverage run -m pytest -v -s'
                sh 'coverage report -m'
            }
        }
    }
}