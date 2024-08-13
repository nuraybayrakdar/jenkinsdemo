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
        stage('Docker Image Scan Trivy') {
            steps {
                script {
                    try {
                        def trivyOutput = sh(script: "trivy image ${REPO_NAME}:${BUILD_NUMBER}", returnStdout: true).trim()

                        println trivyOutput

                        if (trivyOutput.contains("CRITICAL") || trivyOutput.contains("HIGH")) {
                            echo "Trivy found vulnerabilities but continuing the build."
                            // Optionally, you can also add a warning or report here.
                        } else {
                            echo "No vulnerabilities found"
                        }
                    } catch (Exception e) {
                        echo "Trivy scan failed: ${e.message}"
                        // Continue the pipeline even if there is an error in the Trivy scan.
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
