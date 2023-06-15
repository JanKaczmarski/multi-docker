pipeline {
    agent any
    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }
    environment {
        DOCKER_PASSWORD = credentials('DOCKER_PASSWORD')
        DOCKER_LOGIN = credentials('DOCKER_LOGIN')
    }
    triggers {
        pollSCM 'H/5 * * * *'
    }
    stages {
        stage('before_install'){
            steps {
                sh '''
                docker build -t bigjack213/react-test -f ./client/Dockerfile.dev ./client
                '''
            }
        }
        stage ('Test') {
            steps {
                sh '''
                docker run bigjack213/react-test npm test -- --coverage
                '''
            }
        }
        stage ('Push to docker hub') {
            steps {
                sh '''
                docker build -t bigjack213/multi-client ./client
                docker build -t bigjack213/multi-nginx ./nginx
                docker build -t bigjack213/multi-server ./server
                docker build -t bigjack213/multi-worker ./worker
                // Log in to hte docker CLI
                echo $DOCKER_PASSWORD | docker login -u $DOCKER_LOGIN --password-stdin
                // Take thos images and push them to docker hub
                docker push bigjack213/multi-client
                docker push bigjack213/multi-nginx
                docker push bigjack213/multi-server
                docker push bigjack213/multi-worker
                '''
            }
        }
    }
}