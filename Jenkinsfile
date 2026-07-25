pipeline {
    agent any
    environment {
        IMAGE_NAME = "Shaam1995/nginx-version"
        APP_SERVER = "ubuntu@18.217.144.247"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Shaam1995/high_available_terraform_with_jenkins.git'
            }
        }
        stage('Build Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'sminathan', passwordVariable: '8489331326')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push $IMAGE_NAME:latest'
                }
            }
        }
        stage('Deploy to App Server') {
            steps {
                sshagent(credentials: ['dev']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no $APP_SERVER '
                            docker pull $IMAGE_NAME:latest &&
                            docker stop nginx-app || true &&
                            docker rm nginx-app || true &&
                            docker run -d --name nginx-app -p 80:80 $IMAGE_NAME:latest
                        '
                    """
                }
            }
        }
    }
