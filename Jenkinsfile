pipeline {

    agent any

    environment {

        IMAGE_NAME = "chintanchaudhari/gocart"
        TAG = "${BUILD_NUMBER}"
        NAMESPACE = "gocart"
    }

    stages {

        stage('Clone') {

            steps {

                git branch: 'main',
                url: 'https://github.com/Chintan-Chaudhari/gocart.git'
            }
        }

        stage('Build Docker Image') {

            steps {

                sh 'docker build -t $IMAGE_NAME:$TAG .'
            }
        }

        stage('Push Docker Image') {

            steps {

                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh '''
                    echo $DOCKER_PASS | docker login \
                    -u $DOCKER_USER --password-stdin

                    docker push $IMAGE_NAME:$TAG
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {

            steps {

                sh '''
                kubectl set image deployment/gocart \
                gocart=$IMAGE_NAME:$TAG \
                -n $NAMESPACE

                kubectl rollout status deployment/gocart \
                -n $NAMESPACE
                '''
            }
        }
    }
}
