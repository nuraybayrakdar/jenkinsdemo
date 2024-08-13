pipeline {
    agent any
    
    enviroment{
        DOCKER_CREDENTIALS_ID = 'docker-id'
        REPO_NAME = 'nuraybayrakdar/repo1'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/nuraybayrakdar/website.git', credentialsId: 'GitHub-id'
            }
        }
        stage('Docker Image Build') {
            steps {
               script {
                    docker.build("nuraybayrakdar/repo1:${BUILD_NUMBER}")
                }
            }
        }
        
        stage('Docker Image Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        docker.image("nuraybayrakdar/repo1:${BUILD_NUMBER}").push('latest')
                    }
                }
            }
        }
    }   
}