pipeline {
    agent any

    environment {
        DOCKERHUB_CRED = credentials('dockerhub-token') // create Jenkins credential
        IMAGE_NAME = "hamza/static-site"
    }

    stages {

        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/hamza5839/static-site.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Test') {
            steps {
                echo 'No real tests for static site, just confirming Docker image is built'
                sh 'docker images'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-token', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push $IMAGE_NAME:latest
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying container on server'
                sh '''
                docker stop static-site || true
                docker rm static-site || true
                docker run -d --name static-site -p 80:80 $IMAGE_NAME:latest
                '''
            }
        }

    }
}
