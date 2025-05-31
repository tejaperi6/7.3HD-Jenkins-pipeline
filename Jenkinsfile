pipeline {
    agent any

    environment {
        SONARQUBE = 'SonarQubeServer'             // Jenkins SonarQube server name
        SONAR_TOKEN = credentials('sonar-token')  // Jenkins secret text credential ID for SonarQube token
        SNYK_TOKEN = credentials('snyk-token')    // Jenkins secret text credential ID for Snyk token (Security scan)
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
        }

        stage('Security Scan') {
            steps {
                script {
                    // Assuming snyk is installed on Jenkins agent; otherwise install it in agent setup
                    sh "snyk auth $SNYK_TOKEN"
                    sh 'snyk test || true'  // Don't fail pipeline immediately on vulnerabilities (adjust as needed)
                }
            }
        }

        stage('Deploy to Test') {
            steps {
                script {
                    // Adjust according to your test deploy environment, e.g. docker-compose or kubectl
                    sh 'docker-compose -f docker-compose.test.yml up -d'
                }
            }
        }

        stage('Release to Prod') {
            steps {
                script {
                    // Replace with your production deploy script or commands
                    sh './deploy_prod.sh'
                }
            }
        }

        stage('Monitoring & Alerting') {
            steps {
                script {
                    echo 'Monitoring is set up externally via Datadog/New Relic/etc.'
                    // Optionally call APIs or run monitoring checks here
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
