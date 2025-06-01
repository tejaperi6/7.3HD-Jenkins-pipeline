pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh './gradlew clean bootJar'
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

        stage('Security') {
            steps {
                sh './gradlew dependencyCheckAnalyze'
            }
            post {
                always {
                    dependencyCheckPublisher pattern: '**/build/reports/dependency-check-report.xml'
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                // Example deploy: run jar in background (replace with your real deployment)
                sh 'nohup java -jar build/libs/*.jar &'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        failure {
            mail to: 'saiteja.phano@gmail.com',
                 subject: "Build failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Check Jenkins logs at ${env.BUILD_URL}"
        }
    }
}
