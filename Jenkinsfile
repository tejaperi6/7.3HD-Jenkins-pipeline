pipeline {
    agent any

    environment {
        GIT_CREDENTIALS_ID = 'github-creds'
        JAR_NAME = 'jenkins-pipeline-example.jar'
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

        stage('Test') {
            steps {
                sh './gradlew test integrationTest'
                junit '**/build/test-results/test/TEST-*.xml'
            }
        }

        stage('Deploy') {
            steps {
                echo "Simulating deployment to staging environment..."
                echo "Deploying ${env.JAR_NAME} to test server or local environment"
            }
        }

        stage('Release') {
            steps {
                echo "Releasing application to production"
                echo "Simulated release successful!"
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
        }
    }


}
