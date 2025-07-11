pipeline {
    agent any

    environment {
        IMAGE_NAME = 'yourdockerhubusername/ci-cd-demo-app'
    }

    stages {
        stage('Clone Repo') {
            steps {
                echo 'Code cloned automatically by Jenkins (via Git URL)'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $IMAGE_NAME
                    '''
                }
            }
        }

        stage('Deploy Locally') {
            steps {
                sh '''
                    docker stop my-static-site || true
                    docker rm my-static-site || true
                    docker run -d --name my-static-site -p 8080:80 $IMAGE_NAME
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployment completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed somewhere. Check logs.'
        }
    }
}
