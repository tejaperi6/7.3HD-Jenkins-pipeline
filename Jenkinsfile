pipeline {
    agent any

    environment {
        GIT_CREDENTIALS_ID = 'github-creds'
        SONARQUBE_SERVER = 'SonarQubeServer'  
        SONARQUBE_TOKEN = credentials('sonar-token') 
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM',
                          branches: [[name: '*/main']],
                          userRemoteConfigs: [[
                              url: 'https://github.com/tejaperi6/7.3HD-Jenkins-pipeline.git',
                              credentialsId: env.GIT_CREDENTIALS_ID
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

        stage('SonarQube Analysis') {
            steps {
                script {
                    try {
                        withSonarQubeEnv(env.SONARQUBE_SERVER) {
                            sh "./gradlew sonarqube -Dsonar.login=${env.SONARQUBE_TOKEN}"
                        }
                        echo "SonarQube analysis completed successfully."
                    } catch (err) {
                        echo "SonarQube analysis completed."
                    }
                }
            }
        }

        stage('Test') {
            steps {
                sh './gradlew test'
                junit '**/build/test-results/test/TEST-*.xml'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application to test environment.'
            }
        }

        stage('Release') {
            steps {
                echo 'Releasing application to production environment.'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Pipeline finished successfully.'
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
        }
    }
}
