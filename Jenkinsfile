pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "ansupatel27999/myapp"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/ansupatel27999/my-app.git'
            }
        }

        stage('Terraform Init & Apply') {
            steps {
                dir('terraform') {
                    sh 'terraform init'
                    sh 'terraform apply -auto-approve'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USERNAME',
                    passwordVariable: 'PASSWORD'
                )]) {
                    sh '''
                      echo $PASSWORD | docker login -u $USERNAME --password-stdin
                      docker push $DOCKER_IMAGE
                    '''
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                  docker stop myapp || true
                  docker rm myapp || true
                  docker run -d -p 5000:5000 --name myapp $DOCKER_IMAGE
                '''
            }
        }
    }
}
