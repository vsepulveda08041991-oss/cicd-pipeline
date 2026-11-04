pipeline {
    agent any
    tools {
        nodejs 'node-lts'
    }
    stages {
        stage('Configurar Puertos por Rama') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        env.APP_PORT = '3000'
                        env.IMAGE_TAG = 'nodemain:v1.0'
                        echo "Configurando entorno de Producción (main) en el puerto ${env.APP_PORT}"
                    } else if (env.BRANCH_NAME == 'dev') {
                        env.APP_PORT = '3001'
                        env.IMAGE_TAG = 'nodedev:v1.0'
                        echo "Configurando entorno de Desarrollo (dev) en el puerto ${env.APP_PORT}"
                    } else {
                        // Por defecto si se ejecuta local o en otra rama
                        env.APP_PORT = '3000'
                        env.IMAGE_TAG = 'node-app:latest'
                    }
                }
            }
        }
        stage('Verificar Herramientas') {
            steps {
                bat 'node -v'
                bat 'npm -v'
                bat 'docker version'
            }
        }
        stage('Ejecutar Pruebas') {
            steps {
                bat 'npm test'
            }
        }
        stage('Construir Imagen Docker') {
            steps {
                bat "docker build -t ${env.IMAGE_TAG} ."
            }
        }
        stage('Desplegar Contenedor') {
            steps {
                script {
                    try { 
                        bat "docker stop app-${env.BRANCH_NAME ?: 'local'} && docker rm app-${env.BRANCH_NAME ?: 'local'}" 
                    } catch(e) {}
                    
                    bat "docker run -d -p ${env.APP_PORT}:3000 --name app-${env.BRANCH_NAME ?: 'local'} ${env.IMAGE_TAG}"
                }
            }
        }
    }
}