pipeline {
    agent { label 'for-testing' }

    stages {
        stage('Step 1: Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Step 2: Install Dependencies') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    python3 -m pip install --upgrade pip
                    if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
                    pip install ruff pytest coverage
                '''
            }
        }

        stage('Step 3: Lint with Ruff') {
            steps {
                script {
                    // Dùng catchError để giống "continue-on-error: true" trong GitHub Actions
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        sh '''
                            . venv/bin/activate
                            ruff --target-version=py310 .
                        '''
                    }
                }
            }
        }

        stage('Step 4: Test with Pytest') {
            steps {
                sh '''
                    . venv/bin/activate
                    coverage run -m pytest -v -s
                '''
            }
        }

        stage('Step 5: Coverage Report') {
            steps {
                sh '''
                    . venv/bin/activate
                    coverage report -m
                '''
            }
        }
    }

    post {
        always {
            echo 'Quy trình CI hoàn tất!'
        }
    }
}