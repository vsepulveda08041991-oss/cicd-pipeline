pipeline {
    agent any
    tools {
        nodejs 'node-lts'
    }
    stages {
        stage('Build') {
            steps {
                bat 'npm install'
            }
        }
        stage('Test') {
            steps {
                bat 'npm test'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        env.IMAGE_TAG = 'nodemain:v1.0'
                    } else if (env.BRANCH_NAME == 'dev') {
                        env.IMAGE_TAG = 'nodedev:v1.0'
                    } else {
                        env.IMAGE_TAG = 'node-app:latest'
                    }
                    bat "docker build -t ${env.IMAGE_TAG} ."
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        env.APP_PORT = '3000'
                        env.IMAGE_TAG = 'nodemain:v1.0'
                    } else if (env.BRANCH_NAME == 'dev') {
                        env.APP_PORT = '3001'
                        env.IMAGE_TAG = 'nodedev:v1.0'
                    }
                    
                    try { 
                        bat "docker stop app-${env.BRANCH_NAME} && docker rm app-${env.BRANCH_NAME}" 
                    } catch(e) {}
                    
                    bat "docker run -d -p ${env.APP_PORT}:3000 --name app-${env.BRANCH_NAME} ${env.IMAGE_TAG}"
                }
            }
        }
    }
}