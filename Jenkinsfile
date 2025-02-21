pipeline {
    agent any
    tools {
        maven 'M2_HOME'
    }
    environment {
        AWS_REGION = 'us-east-1'
        BRANCH_NAME = 'main'
        SCANNER_HOME = tool 'sonar-tool'
        // GITHUB_CREDENTIALS = 'github-cred'
        GIT_URL = 'https://github.com/henrykrop2022/geoapp.git'
        SONARQUBE_CRED = 'sonar-cred'
        SONARQUBE_INSTALLATION = 'sonar-server'
        APP_NAME = 'geoapp'
        JFROG_CRED = 'jfrog-cred'
        ARTIFACTORYPATH = 'target/*.jar'
        ARTIFACTORY_URL = 'http://ec2-52-87-245-70.compute-1.amazonaws.com:8081/artifactory'
        REPO = 'geolocation'
        ARTIFACTORYTARGETPATH = "release_${BUILD_ID}.jar"
        DOCKER_REPO = '180294207776.dkr.ecr.us-east-1.amazonaws.com/geolocation'
        REPO_URL = '180294207776.dkr.ecr.us-east-1.amazonaws.com'
       
    }
    stages {
        stage('git checkout') {
            steps {
                // git branch: "${BRANCH_NAME}", credentialsId: "${GITHUB_CREDENTIALS}", \
                // url: "${GIT_URL}"
                git branch: "${BRANCH_NAME}", url: "${GIT_URL}"
            }
        }
        stage('unit test') {
            steps {
                sh 'mvn clean'
                sh 'mvn test'
                sh 'mvn compile'
            }
        }
    
//         stage('sonarqube scan') {
//             steps {
//                 withSonarQubeEnv(credentialsId: "${SONARQUBE_CRED}", \
//                 installationName: "${SONARQUBE_INSTALLATION}") {
//                     sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=${APP_NAME} -Dsonar.projectKey=${APP_NAME} -Dsonar.java.binaries=. '''
//                 }
//             }
//         }
//         stage('Quality Gate Check') {
//             steps {
//                 script {
//                     waitForQualityGate abortPipeline: false, credentialsId: "${SONARQUBE_CRED}"
//                 }
                
//             }
//         }
        
        stage('Trivy Scan') {
            steps {
                sh "trivy fs --format table -o maven_dependency.html ."
            }
        }
        stage('code packaging') {
            steps {
                sh 'mvn package'
            }
        }
        stage('upload Jar to Jfrog') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${JFROG_CRED}", usernameVariable: 'ARTIFACTORY_USER', passwordVariable: 'ARTIFACTORY_PASSWORD')]) {
                    script {
                        sh """ curl -u ${ARTIFACTORY_USER}:${ARTIFACTORY_PASSWORD} -T ${ARTIFACTORYPATH} \
                        ${ARTIFACTORY_URL}/${REPO}/${ARTIFACTORYTARGETPATH} """                    
                    } 
                }
            }
        }
        stage('Docker Image Build') {
            steps {
                script {
                    sh "docker build  --no-cache -t ${DOCKER_REPO} ."
                    sh "docker build --no-cache -t  ${DOCKER_REPO}:${BUILD_ID} ."

                }
            }
        }
        stage('Scan Docker Image') {
            steps {
                script {
                    sh """trivy image --format table -o docker_image_report.html ${DOCKER_REPO}:${BUILD_ID}"""
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${REPO_URL}"
                    sh " docker push ${DOCKER_REPO}:latest"
                    sh " docker push ${DOCKER_REPO}:${BUILD_ID}"
                }
            }
        }
     }
}
