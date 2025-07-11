pipeline {
    agent any

    environment {
        IMAGE_NAME = 'elvinsingh/ci-cd-demo-app'
    }

    stages {
        stage('Clone Repo') {
            steps {
                echo 'Code cloned automatically by Jenkins (via Git URL)'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat '''
                        echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                        docker push %IMAGE_NAME%
                    '''
                }
            }
        }

        stage('Deploy Locally') {
            steps {
                bat '''
                    docker stop ci-cd-demo-app || exit 0
                    docker rm ci-cd-demo-app || exit 0
                    docker rmi %IMAGE_NAME% || exit 0
                    docker run -d --name ci-cd-demo-app -p 8080:80 %IMAGE_NAME%
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
