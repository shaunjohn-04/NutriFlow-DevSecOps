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

        stage('Frontend Dependencies') {
            steps {
                echo 'Installing frontend dependencies'

                sh '''
                    docker run --rm \
                    -v "$WORKSPACE/frontend:/app" \
                    -w /app \
                    node:22-alpine \
                    sh -c "npm ci"
                '''
            }
        }

        stage('Frontend Lint') {
            steps {
                echo 'Running ESLint code quality analysis'

                catchError(
                    buildResult: 'UNSTABLE',
                    stageResult: 'UNSTABLE'
                ) {
                    sh '''
                        docker run --rm \
                        -v "$WORKSPACE/frontend:/app" \
                        -w /app \
                        node:22-alpine \
                        sh -c "npm run lint"
                    '''
                }
            }
        }

        stage('Frontend Build') {
            steps {
                echo 'Building NutriFlow frontend'

                sh '''
                    docker run --rm \
                    -v "$WORKSPACE/frontend:/app" \
                    -w /app \
                    node:22-alpine \
                    sh -c "npm run build"
                '''
            }
        }

        stage('SonarQube Cloud Analysis') {
            steps {
                echo 'Running SonarQube Cloud analysis'

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
                echo 'Building NutriFlow backend Docker image'

                dir('backend') {
                    sh '''
                        docker build \
                        -t ${BACKEND_IMAGE}:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Frontend Docker Build') {
            steps {
                echo 'Building NutriFlow frontend Docker image'

                dir('frontend') {
                    sh '''
                        docker build \
                        -t ${FRONTEND_IMAGE}:${BUILD_NUMBER} .
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'NutriFlow CI pipeline completed successfully!'
        }

        unstable {
            echo 'NutriFlow pipeline completed with code quality warnings. Review ESLint findings.'
        }

        failure {
            echo 'NutriFlow CI pipeline failed. Check the console output.'
        }

        always {
            echo 'NutriFlow pipeline execution finished.'
        }
    }
}
