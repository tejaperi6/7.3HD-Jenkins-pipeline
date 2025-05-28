pipeline {
    agent any
    environment {
        SONARQUBE = 'SonarQube'  // Make sure this matches your Jenkins SonarQube config
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/tejaperi6/7.3HD-Jenkins-pipeline.git'
            }
        }
        stage('Build') {
            steps {
                sh './gradlew clean build'
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
                withSonarQubeEnv('SonarQube') {
                    sh './gradlew sonarqube'
                }
            }
        }
    }
}
