pipeline {
    agent {
        docker {
            image 'docker:latest'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
        DOCKER_IMAGE = 'divya16112002/jenkins'
        
    }

    parameters {
        string(
            name: 'IMAGE_TAG', 
            defaultValue: 'v0.1', 
            description: 'Docker image tag version'
        )
    }

    
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', 
                url: 'https://github.com/DivyaDarshanTiwari/fastapi-dockerize-jenkins.git'
            }
        }
        
        stage('Verify Files') {
            steps {
                sh '''
                    echo "Checking project files..."
                    ls -la
                    pwd
                    echo "\n=== main.py ==="
                    cat main.py
                    echo "\n=== Dockerfile ==="
                    cat Dockerfile
                    echo "\n=== requirements.txt ==="
                    cat requirements.txt
                '''
            }
        }

        stage('Check Docker Access') {
            steps {
                sh 'docker --version'
            }
        }
        
        stage('Build & Push Docker Image') {
            steps {
                    script {
                        def fullTag = "${env.DOCKER_IMAGE}:${params.IMAGE_TAG}-${env.BUILD_NUMBER}"
    
                        withCredentials([usernamePassword(credentialsId: 'dockerhub-credential', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                            sh """
                                echo "Logging into Docker Hub..."
                               echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                               echo "Building Docker image with tag: ${fullTag}"
                               docker build -t ${fullTag} .
                               echo "Pushing image to Docker Hub..."
                               docker push ${fullTag}
                           """
                       }
                    }

            }
        }
    }
    
    post {
        always {
            sh 'docker logout'
            cleanWs()
        }
        success {
            echo "Docker image successfully built and pushed!"
        }
        failure {
            echo "Pipeline failed. Check logs for details."
        }
    }
}