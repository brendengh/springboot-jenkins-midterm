pipeline {
    agent any

    options {
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                sh 'git log -1 --oneline'
            }
        }

        stage('Build') {
            steps {
                sh 'chmod +x gradlew'
                // bootJar packages the app and all dependencies into one executable fat jar
                sh './gradlew clean bootJar --no-daemon'
            }
        }

        stage('Test') {
            steps {
                sh './gradlew test --no-daemon'
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: 'build/test-results/test/*.xml'
                }
            }
        }

        stage('Archive') {
            steps {
                sh 'ls -lh build/libs/'
                archiveArtifacts artifacts: 'build/libs/*.jar', excludes: 'build/libs/*-plain.jar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'Fat jar built and archived: build/libs/demo-0.0.1-SNAPSHOT.jar'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
