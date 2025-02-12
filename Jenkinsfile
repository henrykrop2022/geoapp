pipeline {
    agent any
    environment {
        BRANCH_NAME = 'main'
        GITHUB_CREDENTIALS = 'github-credentials'
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
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}