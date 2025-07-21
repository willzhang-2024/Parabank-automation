pipeline {
    agent any

    triggers {
        cron('0 14 * * *')  // Runs daily at 2:00 PM
    }

    environment {
        // Update path as needed
        JAVA_HOME = 'C:\\Program Files\\Java\\jdk-21'
        PATH = "${JAVA_HOME}\\bin;${env.PATH}"
    }

    tools {
        nodejs 'NodeJS_18'       // Define this tool in Jenkins > Global Tool Configuration
        allure 'Allure_2.21.0'   // Define this in Jenkins > Global Tool Configuration
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the Git repo (replace URL with your repo)
                git branch: 'main', url: 'https://github.com/willzhang-2024/Parabank-automation.git'
            }
        }
        stage('Install dependencies') {
            steps {
                // Use pip to install requirements
                bat 'pip install -r requirements.txt'
                bat 'npm install -g allure-commandline --force'
            }
        }
        stage('Install Playwright browsers') {
            steps {
                // Run playwright install
                bat 'playwright install'
            }
        }

        stage('Run Tests') {
            steps {
                // Run pytest
                bat 'pytest --alluredir=allure-results -v'
            }
        }

        stage('Generate Allure Report') {
            steps {
                bat 'allure generate allure-results --clean -o allure-report'
            }
        }

        stage('Archive Reports') {
                steps {
                    archiveArtifacts artifacts: 'allure-report/**/*', fingerprint: true
                    archiveArtifacts artifacts: 'trace/**/*.zip', allowEmptyArchive: true
                }
            }
        }

    post {
        always {
            echo 'Pipeline completed.'
        }
    }
}
