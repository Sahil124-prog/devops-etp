pipeline{
    agent any
    environment{
        CONTAINER_NAME = "express-app-container"
        DOCKER_USER_NAME = "sahilrajput062004"   
        IMAGE_NAME = "devops-etp"                
        IMAGE_TAG = "latest"
        KUBECONFIG       = "C:\\ProgramData\\Jenkins\\.jenkins\\.kube\\config"
    }
    stages{
        stage('Clone code'){
            steps{
                echo 'pulling code...'
                checkout scm
            }
        }

        stage('Build image'){
            steps{
                echo 'Building the image...'
                bat "docker build -t %DOCKER_USER_NAME%/%IMAGE_NAME%:%IMAGE_TAG% ."
            }
        }

        stage('Push to DockerHub'){
            steps{
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat "docker login -u %DOCKER_USER% -p %DOCKER_PASS%"
                    bat "docker push %DOCKER_USER_NAME%/%IMAGE_NAME%:%IMAGE_TAG%"
                }
            }
        }

        stage('Deploy k8s'){
            steps{
                echo "Deploying deployment.yml"
                bat "kubectl apply -f k8s/deployment.yml --validate=false"
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
                echo "Running the container..."
                bat "docker rm -f %CONTAINER_NAME% || exit 0"
                bat "docker run -d --name %CONTAINER_NAME% -p 3000:3000 %DOCKER_USER_NAME%/%IMAGE_NAME%:%IMAGE_TAG%"
            }
        }
    }
    post{
        success{ echo "Pipeline success!" }
        failure{ echo "Pipeline failed." }
    }
}