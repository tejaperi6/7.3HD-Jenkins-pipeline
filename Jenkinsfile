pipeline {
    agent any

    environment {
        GIT_CREDENTIALS = 'github-creds'
        SMTP_CREDENTIALS = 'gmail-smtp'
        RECIPIENTS = 'saiteja.phani@gmail.com'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/tejaperi6/7.3HD-Jenkins-pipeline.git',
                        credentialsId: "${GIT_CREDENTIALS}"
                    ]]
                ])
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
                junit '**/build/test-results/test/*.xml'
            }
        }

        stage('Security') {
            steps {
                script {
                    def result = sh(script: './gradlew dependencyCheckAnalyze', returnStatus: true)
                    if (result != 0) {
                        echo "Security vulnerabilities found! Check reports for details."
                        error "Failing build due to security vulnerabilities."
                    } else {
                        echo "No critical security vulnerabilities found."
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                // Replace this with your actual deployment commands
                // e.g. sh './deploy.sh' or Kubernetes/Docker deploy commands
            }
        }
    }

    post {
        failure {
            mail(
                to: "${RECIPIENTS}",
                subject: "Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Build failed! Check Jenkins logs: ${env.BUILD_URL}"
            )
        }
        always {
            cleanWs()
        }
    }
}
