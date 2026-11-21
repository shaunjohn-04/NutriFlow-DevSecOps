pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REGISTRY = '104360117424.dkr.ecr.ap-south-1.amazonaws.com'

        BACKEND_IMAGE = '104360117424.dkr.ecr.ap-south-1.amazonaws.com/nutriflow-backend'
        FRONTEND_IMAGE = '104360117424.dkr.ecr.ap-south-1.amazonaws.com/nutriflow-frontend'
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
                    sh '''
                        docker build \
                        -t ${BACKEND_IMAGE}:${BUILD_NUMBER} \
                        -t ${BACKEND_IMAGE}:latest .
                    '''
                }
            }
        }

        stage('Frontend Docker Build') {
            steps {
                dir('frontend') {
                    sh '''
                        docker build \
                        --build-arg VITE_API_URL=/nutriflow \
                        -t ${FRONTEND_IMAGE}:${BUILD_NUMBER} \
                        -t ${FRONTEND_IMAGE}:latest .
                    '''
                }
            }
        }

        stage('ECR Login') {
            steps {
                sh '''
                    aws ecr get-login-password \
                    --region ${AWS_REGION} | \
                    docker login \
                    --username AWS \
                    --password-stdin ${ECR_REGISTRY}
                '''
            }
        }

        stage('Push Images to ECR') {
            steps {
                sh '''
                    docker push ${BACKEND_IMAGE}:${BUILD_NUMBER}
                    docker push ${BACKEND_IMAGE}:latest

                    docker push ${FRONTEND_IMAGE}:${BUILD_NUMBER}
                    docker push ${FRONTEND_IMAGE}:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'NutriFlow CI pipeline and ECR publishing completed successfully!'
        }

        unstable {
            echo 'Pipeline completed with code quality warnings. Review ESLint findings.'
        }

        failure {
            echo 'NutriFlow pipeline failed. Check the console output.'
        }

        always {
            echo 'NutriFlow pipeline execution finished.'
        }
    }
}
