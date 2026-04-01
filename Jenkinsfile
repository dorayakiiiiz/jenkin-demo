pipeline {
    agent { label 'for-testing' }

    // Task 5: Tạo tham số lựa chọn yes/no
    parameters {
        choice(name: 'RUN_TEST', choices: ['yes', 'no'], description: 'Sỹ có muốn chạy bộ Test không?')
    }

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
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        sh '''
                            . venv/bin/activate
                            # Sửa lỗi: Bỏ --target-version vì Ruff bản này không hỗ trợ
                            ruff check .
                        '''
                    }
                }
            }
        }

        stage('Step 4: Test & Coverage') {
            // Task 5: Chỉ chạy nếu chọn "yes"
            when {
                expression { return params.RUN_TEST == 'yes' }
            }
            steps {
                sh '''
                    . venv/bin/activate
                    coverage run -m pytest -v -s
                    coverage report -m
                '''
            }
        }
    }
}