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
                // Run PMD and Checkstyle on main and test source
                sh './gradlew pmdMain pmdTest checkstyleMain checkstyleTest'
            }
            post {
                always {
                    recordIssues tools: [pmd(), checkStyle()]
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                // Add your deployment commands here, for example:
                // sh './deploy-script.sh'
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
