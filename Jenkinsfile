pipeline {
    agent any
    environment {
        registry = "leninalcantara/simulador-dados"
        registryCredentials = "dockerhub"
        project = "simulador-dados"
        repository = "https://github.com/leninalcantara/simulador-dados.git"
        repositoryCredentials="github"
    }
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout Code') {
            steps {
                script {
                    git branch: 'main',
                        credentialsId: repositoryCredentials,
                        url: repository
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build registry
                }
            }
        }
        stage('Test Container') {
            steps {
                script {
                    try {
                        sh "docker run --name ${project}-test ${registry}"
                    } finally {
                        sh "docker rm ${project}-test || true"
                    }
                }
            }
        }
        stage('Push to DockerHub') {
            steps {
                script {
                    docker.withRegistry('', registryCredentials) {
                        dockerImage.push("latest")
                    }
                }
            }
        }
        stage('Cleanup Local Image') {
            steps {
                script {
                    sh "docker rmi ${registry} || true"
                }
            }
        }
    }
    post {
        failure {
            echo "El pipeline ha fallado."
        }
    }
}