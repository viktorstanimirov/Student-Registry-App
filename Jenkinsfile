pipeline {
    agent any

    environment {
        NODE_VERSION = '20.x'
        RENDER_API_KEY = credentials('render-api-key') 
        SERVICE_ID = credentials('service-id') 
        GITHUB_TOKEN = credentials('github-token') 
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            agent {
                docker {
                    image "node:${NODE_VERSION}"
                }
            }
            steps {
                script {
                    sh 'npm ci'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    sh 'npx mocha tests/*.js'
                }
            }
        }

        stage('Deploy to Production') {
            steps {
                script {
                    sh """
                    curl -X POST https://api.render.com/deploy \
                    -H "Authorization: Bearer ${RENDER_API_KEY}" \
                    -d service_id=${SERVICE_ID} \
                    -d trigger=manual
                    """
                }
            }
        }
    }

    post {
        failure {
            mail to: 'your-email@example.com',
                 subject: "Build Failed: ${env.JOB_NAME} ${env.BUILD_NUMBER}",
                 body: "Check the Jenkins build at ${env.BUILD_URL}"
        }
    }
}
