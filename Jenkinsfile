pipeline {
    agent any

    environment {
       
        JENKINS_NODE_COOKIE = 'dontKillMe'
        BUILD_ID = 'dontKillMe'
    }

    stages {
        stage('1. Checkout') {
            steps {
                echo '=================================================='
                echo 'STAGE 1: Checking out code from repository...'
                echo "Building commit: ${env.GIT_COMMIT ?: 'Local Commit'}"
                echo '=================================================='
            }
        }

        stage('2. Install & Build') {
            steps {
                echo '=================================================='
                echo 'STAGE 2: Installing dependencies and building...'
                echo '=================================================='
                sh 'npm install'
                sh 'npm run build'
            }
        }

        stage('3. Automated Tests') {
            steps {
                echo '=================================================='
                echo 'STAGE 3: Running automated unit & API tests...'
                echo '=================================================='
                sh 'npm test'
            }
        }

        stage('4. Deploy to Staging (Port 3001)') {
            steps {
                echo '=================================================='
                echo 'STAGE 4: Deploying application to STAGING on Port 3001...'
                echo '=================================================='
                sh 'chmod +x ./scripts/deploy-staging.sh'
                sh 'JENKINS_NODE_COOKIE=dontKillMe BUILD_ID=dontKillMe ./scripts/deploy-staging.sh'
            }
        }

        stage('5. Smoke Test Staging') {
            steps {
                echo '=================================================='
                echo 'STAGE 5: Verifying Staging Deployment on Port 3001...'
                echo '=================================================='
                sh 'chmod +x ./scripts/health-check.sh'
                sh './scripts/health-check.sh 3001'
            }
        }

        stage('6. Deploy to Production (Port 3000)') {
            steps {
                echo '=================================================='
                echo 'STAGE 6: Promoting build to PRODUCTION on Port 3000...'
                echo '=================================================='
                sh 'chmod +x ./scripts/deploy-prod.sh'
                sh 'JENKINS_NODE_COOKIE=dontKillMe BUILD_ID=dontKillMe ./scripts/deploy-prod.sh'
            }
        }
    }

    post {
        success {
            echo '=================================================='
            echo 'PIPELINE SUCCESSFUL!'
            echo 'Production UI: http://localhost:3000'
            echo 'Staging UI: http://localhost:3001'
            echo '=================================================='
        }
        failure {
            echo '=================================================='
            echo 'PIPELINE FAILED! Fix the breaking tests or build error and push again.'
            echo '=================================================='
        }
    }
}
