pipeline {
    agent any
    
    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub_id'  
        REPO_NAME = 'nuraybayrakdar/repo1' 
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
                    snykSecurity(
                        snykInstallation: 'synk@latest',
                        snykTokenId: 'SNYK_TOKEN',
                        
                    )
                

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
        stage('Docker Image Scan Trviy'){
            steps {
                script{
                    def trivyOutput = sh(script: "trivy image $REPO_NAME:latest", returnStdout: true).trim()

                    println trivyOutput

                    if (trivyOutput.contains("CRITICAL") || trivyOutput.contains("HIGH")) {
                        error("Trivy found vulnerabilities")
                    } else {
                        echo "No vulnerabilities found"
                    }
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
