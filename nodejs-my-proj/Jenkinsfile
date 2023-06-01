pipeline {

    agent {
        label 'static-slaves'
    }

    tools {
        nodejs 'nodejs'
        dockerTool 'docker-default'
    }

    triggers {
        githubPush()
    }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('my_dockerhub_creds')
        IMAGE_NAME = 'dmanov/nodejs-app'
    }

    stages {

        stage('Clean') {
            steps {
                cleanWs()
            }
        }

        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/Dma98/nodejs-my-proj'
            }
        }
        stage('Build') {
            steps {
                sh 'npm ci' //This is for building the nodejs project
            }
        }
        stage('Test') {
            steps {
                sh 'npm test' //This is for testing the nodejs modules
            }
        }
        stage('Docker build') {
            steps {
                sh 'docker build -t ${IMAGE_NAME} -f Dockerfile .'
                sh 'docker tag ${IMAGE_NAME} ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
        stage('Docker push') {
            steps {
                sh 'docker login -u ${DOCKERHUB_CREDENTIALS_USR} -p ${DOCKERHUB_CREDENTIALS_PSW}'
                sh 'docker push ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
        // stage('Deploy') {
        //    steps {
        //         sh 'pkill node | true'
        //         sh 'npm install -g forever'
        //         sh 'forever start src/index.js'
        //    }
        // }
        stage('Deploy') {
            steps {
                sh 'docker container rm -f nodejs-app || true'
                sh 'docker run -d -p 4000:3000 --name=nodejs-app ${IMAGE_NAME}'
            }
        }
    }
}