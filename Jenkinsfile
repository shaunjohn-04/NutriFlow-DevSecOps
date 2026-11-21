
pipeline {
    agent any

    environment {
        BACKEND_IMAGE = 'nutriflow-backend'
        FRONTEND_IMAGE = 'nutriflow-frontend'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out NutriFlow source code'
                checkout scm
            }
        }

        stage('Frontend Lint') {
            steps {
                dir('frontend') {
                    sh 'npm ci'
                    sh 'npm run lint'
                }
            }
        }

        stage('Frontend Build') {
            steps {
                dir('frontend') {
                    sh 'npm run build'
                }
            }
        }

        stage('SonarQube Cloud Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner'

                    withSonarQubeEnv('SonarCloud') {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }

        stage('Backend Docker Build') {
            steps {
                dir('backend') {
                    sh 'docker build -t ${BACKEND_IMAGE}:${BUILD_NUMBER} .'
                }
            }
        }

        stage('Frontend Docker Build') {
            steps {
                dir('frontend') {
                    sh 'docker build -t ${FRONTEND_IMAGE}:${BUILD_NUMBER} .'
                }
            }
        }
    }

    post {
        success {
            echo 'NutriFlow CI pipeline completed successfully!'
        }

        failure {
            echo 'NutriFlow CI pipeline failed. Check the console output.'
        }
    }
}

