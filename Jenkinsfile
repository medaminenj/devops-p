pipeline {
    agent any

    tools {
        jdk 'JAVA_17'
        maven 'MAVEN_3'
    }

    stages {
        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }

        stage('Build & Test Backend') {
            steps {
                dir('backend') {
                    withCredentials([usernamePassword(
                        credentialsId: 'MYSQL_CREDENTIALS',
                        usernameVariable: 'MYSQL_USER',
                        passwordVariable: 'MYSQL_PASSWORD'
                    )]) {
                        sh 'mvn clean test package'
                    }
                }
            }
        }

        stage('Test Frontend') {
            steps {
                dir('frontend') {
                    // Install Node dependencies and run tests headlessly
                    sh 'npm install'
                    sh 'npx ng test --watch=false --browsers=ChromeHeadless'
                }
            }
        }

        stage('Deploy Application') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'MYSQL_CREDENTIALS',
                    usernameVariable: 'MYSQL_USER',
                    passwordVariable: 'MYSQL_PASSWORD'
                )]) {
                    sh 'docker-compose down'
                    sh 'docker-compose up -d --build'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed and application deployed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check build logs for details.'
        }
    }
}