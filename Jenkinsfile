pipeline {
    agent any
    
    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub_id'  
        REPO_NAME = 'nuraybayrakdar/repo1' 
        SNYK_TOKEN = credentials('synkid')    
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/nuraybayrakdar/website.git', credentialsId: 'GitHub-id'
            }
        }
        stage('Synk Code Scanning'){
            steps {
                script {
                    withCredentials([string(credentialsId: 'synkid', variable: 'SNYk_TOKEN')]) {
                        sh 'snyk auth $SNYK_TOKEN'
                        sh 'snyk test --all-projects'
                    }

                }
            }
        }

        stage('Docker Image Build') {
            steps {
                script {
                    docker.build("${REPO_NAME}:${BUILD_NUMBER}")
                }
            }
        }
        
        stage('Docker Image Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        docker.image("${REPO_NAME}:${BUILD_NUMBER}").push('latest')
                    }
                }
            }
        }
    }   
}
