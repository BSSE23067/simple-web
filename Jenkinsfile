pipeline {
    agent any
    environment {
        IMAGE = "toobz27/simple-web"
        TAG = "1.0-${env.BUILD_NUMBER}"
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/BSSE23067/simple-web.git', branch: 'main'
            }
        }
        stage('Build') {
            steps {
                sh 'docker build -t $IMAGE:$TAG .'
            }
        }
        stage('Test') {
            steps {
                sh 'echo "no tests - smoke OK"'
            }
        }
        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds',
                    usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {

                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $IMAGE:$TAG
                    '''
                }
            }
        }
        stage('Deploy to k8s') {
            steps {
                sh '''
                kubectl set image deployment/simple-web simple-web=$IMAGE:$TAG --record || true
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                '''
            }
        }
    }
}
