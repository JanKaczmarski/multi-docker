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
                docker build -t $DOCKER_LOGIN/react-test -f ./client/Dockerfile.dev ./client
                '''
            }
        }
        // stage ('Test') {
        //     steps {
        //         sh '''
        //         docker run $DOCKER_LOGIN/react-test npm test
        //         '''
        //     }
        // }
        stage ('Push to docker hub') {
            steps {
                sh '''
                docker build -t $DOCKER_LOGIN/multi-nginx ./nginx
                docker build -t $DOCKER_LOGIN/multi-server ./server
                docker build -t $DOCKER_LOGIN/multi-client ./client
                docker build -t $DOCKER_LOGIN/multi-worker ./worker'''
                // Log in to hte docker CLI
                sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_LOGIN --password-stdin'
                // Take thos images and push them to docker hub
                sh '''
                docker push $DOCKER_LOGIN/multi-nginx
                docker push $DOCKER_LOGIN/multi-server
                docker push $DOCKER_LOGIN/multi-client
                docker push $DOCKER_LOGIN/multi-worker
                '''
            }
        }
    }
    post {
        always {
            sh 'docker logout'
        }
    }
}