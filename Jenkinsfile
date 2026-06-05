pipeline{
    agent any
    environment{
        CONTAINER_NAME = "express-app-container"
        IMAGE_NAME = "node"
        IMAGE_TAG = "18-alpine"
    }
    stages{
        stage('Clone code'){
            steps{
            echo 'pulling code...'
            checkout scm
            }
        }
        stage('bulding image'){
            steps{
            echo 'bulding the image...'
            bat "docker build -t %IMAGE_NAME%:%IMAGE_TAG% ."
            }
        }

        stage('Push to dockerHub'){
            steps{
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-credentials',
                    usernameVariable : 'DOCKER_USER',
                    passwordVariable : 'DOCKER_PASS'
                )])
                {
                    bat "docker login -u %DOCKER_USER% -p %DOCKER_PASS%"
                    bat "docker push %IMAGE_NAME%:%IMAGE_TAG%"
                }
            }
        }
        stage('Deploy k8s'){
            steps{
                echo "Deplying deployment.yml"
                bat "kubectl apply -f k8s/deployment.yml"
            }
        }

        stage('Verify Pods'){
            steps{
                echo "Verifying the pods..."
                bat "kubectl get pods"
                bat "kubectl get services"
            }
        }

        stage('Running the container'){
            steps{
                echo "running the container..."
                bat "docker run -d --name %CONTAINER_NAME% -p 3000:3000 %IMAGE_NAME%:%IMAGE_TAG%"
            }
        }
    }
    post{
        success{
            echo "building pipeline success..."
        }
        failure{
            echo "building pipeline failure..."
        }
    }
}