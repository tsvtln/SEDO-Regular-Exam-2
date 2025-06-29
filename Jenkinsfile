pipeline {
    agent any

    triggers {
        pollSCM('* * * * *')
    }

    environment {
        DOTNET_VERSION = '8.0'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup .NET SDK') {
            steps {
                sh 'dotnet --version || sudo apt update && sudo apt install -y dotnet-sdk-8.0'
            }
        }

        stage('Restore dependencies') {
            steps {
                sh 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                sh 'dotnet build --no-restore'
            }
        }

        stage('Test') {
            steps {
                sh 'dotnet test --no-build --verbosity normal'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
    }

    // Only run this pipeline on develop or feature/* branches
    options {
        skipDefaultCheckout(true)
    }

    // Custom checkout and branch filtering
    stages {
        stage('Conditional Branch Filter') {
            when {
                anyOf {
                    branch 'develop'
                    expression { return env.BRANCH_NAME?.startsWith('feature/') }
                }
            }
            steps {
                echo "Running pipeline for branch ${env.BRANCH_NAME}"
            }
        }
    }
}
