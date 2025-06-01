pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('sonar-token')  // If you want to add SonarQube later, optional
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
                sh './gradlew pmd'
            }
            post {
                always {
                    recordIssues tools: [pmd(), checkStyle()]
                }
            }
        }

        stage('Security') {
            steps {
                echo 'Running security scan placeholder'
                // Example: Integrate Snyk or Trivy here in real setup
                // For now, just print a message to pass the stage
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                // Add deployment commands here (copy jar, deploy to server, etc.)
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
