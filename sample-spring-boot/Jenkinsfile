pipeline {
    agent {
        label 'static-slaves'
    }

    tools {
        gradle 'gradle-default'
        dockerTool 'docker-default'
    }

    triggers {
        githubPush()
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('my_dockerhub_creds')
        IMAGE_NAME = 'dmanov/java-app'
    }

    stages {

        stage('Clean') {
            steps {
                cleanWs()
            }

        }
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Dma98/sample-spring-boot'

            }
        }
        stage('Build') {
            steps {
                sh 'gradle build'
            }
        }
        stage('Test') {
            steps {
                sh 'gradle test'
            }
        }
        stage('Docker-build') {
            steps {
                sh 'docker build -t ${IMAGE_NAME} -f Dockerfile .'
                sh 'docker tag ${IMAGE_NAME} ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
        stage('Docker-push') {
            steps {
                sh 'docker login -u ${DOCKERHUB_CREDENTIALS_USR} -p ${DOCKERHUB_CREDENTIALS_PSW}' 
                sh 'docker push ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker container rm -f java-app || true'
                sh 'docker run -d -p 8090:8080 --name=java-app ${IMAGE_NAME}'
            }
        }

    }
}

