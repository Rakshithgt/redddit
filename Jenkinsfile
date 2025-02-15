pipeline {
    agent any
    tools {
        jdk 'JDK17'
        nodejs 'node 16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        APP_NAME = "reddit-clone-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "rakshithgt96"
        DOCKER_PASS = 'dockerhub'
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
	
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/Rakshithgt/redddit.git'
            }
        }
        stage("Sonarqube Analysis") {
            steps {
                wwithSonarQubeEnv('SonarQube-Server') {
                    sh '''
                     $SCANNER_HOME/bin/sonar-scanner \
                     -Dsonar.projectName=Reddit-Clone-CI \
                     -Dsonar.projectKey=Reddit-Clone-CI
                    '''
                }
            }
        }
        stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'SonarQube-Token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
             }
         }
    }
}
