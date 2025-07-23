pipeline {
    agent any

    environment {
        PYTHON_VERSION = '3.11'
        ALLURE_VERSION = '2.27.0'
    }

    triggers {
        cron('H 14 * * *')  // Runs daily at 2:00 PM
    }

    stages {
        stage('Set up Python') {
            steps {
                bat '''
                    python -m pip install --upgrade pip
                    pip install -r requirements.txt
                    playwright install
                '''
            }
        }

        stage('Download and Unzip Allure CLI') {
            steps {
                bat '''
                    curl -L -o allure.zip https://github.com/allure-framework/allure2/releases/download/%ALLURE_VERSION%/allure-%ALLURE_VERSION%.zip
                    powershell -Command "Expand-Archive -Force 'allure.zip' ."
                '''
            }
        }

        stage('Run Playwright Tests') {
            steps {
                bat '''
                    pytest -v --alluredir=allure-results
                '''
            }
        }

        stage('Run Tests') {
            steps {
                // Run tests and collect Allure results even if they fail
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                    sh 'pytest --alluredir=allure-results'
                }
            }
        }

        stage('Generate Allure Report') {
            // Always generate the report, even if test failed
            steps {
                // `catchError` here is optional unless allure command might fail
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    sh 'allure generate allure-results --clean -o allure-report'
                }
            }
        }

        stage('Archive Allure Report') {
            steps {
                archiveArtifacts artifacts: 'allure-report/**', allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished. Allure report generation attempted.'
        }
    }
}
