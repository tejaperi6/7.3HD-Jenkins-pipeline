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
                    echo "Dependency check completed. Report available at build/reports/dependency-check-report.html"
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                // Add your actual deployment commands here (e.g., docker-compose, kubectl, shell script)
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
