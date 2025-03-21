pipeline {
    agent any 
    
    environment {
        DOCKER_IMAGE = "ruwanthilakshika/nodeapp-cuban"
    }

    stages { 
        stage('SCM Checkout') {
            steps {
                retry(3) {
                    git branch: 'main', url: 'https://github.com/RuwanthiLakshika/CI-CD-Pipeline-with-Docker-and-Jenkins'
                }
            }
        }
        stage('Build Docker Image') {
            steps {  
                script {
                    def buildTag = env.BUILD_NUMBER
                    bat "docker build --no-cache -t ${DOCKER_IMAGE}:${buildTag} ."
                }
            }
        }
        stage('Login to Docker Hub') {
            steps {
                withCredentials([string(credentialsId: 'test-dockerhubpass', variable: 'DOCKER_HUB_PASS')]) {
                    script {
                        def loginSuccess = bat(returnStatus: true, script: "docker login -u ruwanthilakshika -p ${DOCKER_HUB_PASS}")
                        if (loginSuccess != 0) {
                            error("Docker login failed! Check credentials.")
                        }
                    }
                }
            }
        }
        stage('Push Image') {
            steps {
                script {
                    def buildTag = env.BUILD_NUMBER
                    bat "docker push ${DOCKER_IMAGE}:${buildTag}"
                }
            }
        }
    }
    post {
        always {
            bat 'docker logout'
        }
        failure {
            echo "Build failed. Check logs for details."
        }
    }
}
