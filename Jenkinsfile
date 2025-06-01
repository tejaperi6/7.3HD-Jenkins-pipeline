pipeline {
    agent any

    environment {
        SONARQUBE = 'SonarQubeServer'             // Jenkins SonarQube server name
        SONAR_TOKEN = credentials('sonar-token')  // Jenkins secret text credential ID for SonarQube token
    }

    stages {
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
                withSonarQubeEnv(SONARQUBE) {
                    sh "./gradlew sonarqube -Dsonar.login=$SONAR_TOKEN"
                }
            }
            post {
                success {
                    script {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Quality gate failed: ${qg.status}"
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        failure {
            mail to: 'team@example.com',
                 subject: "Build failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Check Jenkins logs at ${env.BUILD_URL}"
        }
    }
}
