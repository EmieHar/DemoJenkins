pipeline {
    agent {
        docker {
            image 'maven:3.6.3-jdk-11' // Utilisez une image avec Maven et JDK
            args '-v /var/run/docker.sock:/var/run/docker.sock' // Permet l'accès au daemon Docker
        }
    }
    environment {
        DOCKER_USERNAME = 'emiliesh'
        GITHUB_REPO_URL = 'https://github.com/EmieHar/DemoJenkins.git'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: "${env.GITHUB_REPO_URL}"
            }
        }

        stage('Clean') {
            steps {
                sh 'mvn clean'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Test') {
            steps {
                script {
                    try {
                        sh 'mvn test'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error "Tests failed!"
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def image = docker.build("${env.DOCKER_USERNAME}/dockercred1:${env.BUILD_NUMBER}", '.')
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'idJenkins') {
                        docker.image("${env.DOCKER_USERNAME}/dockercred1:${env.BUILD_NUMBER}").push()
                    }
                }
            }
        }

        stage('Notify') {
            steps {
                script {
                    if (currentBuild.result == 'SUCCESS') {
                        echo "Build succeeded!"
                    } else {
                        echo "Build failed!"
                    }
                }
            }
        }
    }
}
