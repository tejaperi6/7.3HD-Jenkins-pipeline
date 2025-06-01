pipeline {
    agent any

    environment {
        // Add email recipient here if you want
        NOTIFY_EMAIL = 'saiteja.phano@gmail.com'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh './gradlew clean build'
                archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
            }
        }

        stage('Test') {
            steps {
                sh './gradlew test'
            }
            post {
                always {
                    junit '**/build/test-results/test/*.xml'
                }
            }
        }

        stage('Code Quality') {
            steps {
                sh './gradlew pmd checkstyle'
            }
            post {
                always {
                    recordIssues tools: [pmd(), checkStyle()]
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Running application locally in background...'
                sh 'nohup java -jar build/libs/*.jar > app.log 2>&1 &'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        failure {
            mail to: "${NOTIFY_EMAIL}",
                 subject: "Build failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Check Jenkins logs at ${env.BUILD_URL}"
        }
    }
}
