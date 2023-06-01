pipeline {
    agent {
        label 'static-slaves'
    }

    tools {
        maven 'maven-default'
        dockerTool 'docker-default'
    }

    triggers {
        githubPush()
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('my_dockerhub_creds')
        IMAGE_NAME = 'dmanov/maven-app'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Dma98/spring-with-maven'

            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
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
                sh 'docker container rm -f maven-app || true'
                sh 'docker run -d -p 8091:8080 --name=maven-app ${IMAGE_NAME}'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}

