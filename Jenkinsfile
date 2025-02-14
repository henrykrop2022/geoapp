pipeline {
    agent any
    tools {
        maven 'M2_HOME'
    }
    environment {
        BRANCH_NAME = 'main'
        SCANNER_HOME = tool 'sonar-tool'
        GITHUB_CREDENTIALS = 'github-cred'
        GIT_URL = 'https://github.com/henrykrop2022/geoapp.git'
        SONARQUBE_CRED = 'sonar-cred'
        SONARQUBE_INSTALLATION = 'sonar-server'
        APP_NAME = 'geoapp'
        

    }
    stages {
        stage('git checkout') {
            steps {
                git branch: "${BRANCH_NAME}", credentialsId: "${GITHUB_CREDENTIALS}", \
                url: "${GIT_URL}"
            }
        }
        // stage('unit test') {
        //     steps {
        //         sh 'mvn clean'
        //         sh 'mvn test'
        //         sh 'mvn compile'
        //     }
        // }
        // stage('sonarqube scan') {
        //     steps {
        //         withSonarQubeEnv(credentialsId: "${SONARQUBE_CRED}", \
        //         installationName: "${SONARQUBE_INSTALLATION}") {
        //             sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=${APP_NAME} -Dsonar.projectKey=${APP_NAME} -Dsonar.java.binaries=. '''
        //         }
        //     }
        // }
        stage('Quality Gate Check') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: "${SONARQUBE_CRED}"
                }
                
            }
        }
        stage('Trivy Scan') {
            steps {
                sh 'trivy fs --format table -o maven_dependency.html'
            }
        }
        // stage('code packaging') {
        //     steps {
        //         sh 'mvn package'
        //     }
        // }
        // stage('docker build') {
        //     steps {
        //         sh 'docker build -t geoapp .'
        //     }
        // }
    }
}
