pipeline {
    agent any
    tools {
        maven 'M2_HOME'
    }
    environment {
        BRANCH_NAME = 'main'
        GITHUB_CREDENTIALS = 'github-cred'
        GIT_URL = 'https://github.com/henrykrop2022/geoapp.git'
        // BUILD_NUMBER = '1.0.0'
    }
    stages {
        stage('git checkout') {
            steps {
                git branch: "${BRANCH_NAME}", credentialsId: "${GITHUB_CREDENTIALS}", \
                url: "${GIT_URL}"
            }
        }
        stage('unit test') {
            steps {
                sh 'mvn clean'
                sh 'mvn test'
                sh 'mvn compile'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}
