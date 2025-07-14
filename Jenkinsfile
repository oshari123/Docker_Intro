pipeline {
    agent any

    environment {
        JFROG_DOCKER_REGISTRY = 'trialfwrmrd.jfrog.io' // Example: artifactory.mycompany.com/artifactory/docker-local
        JFROG_CREDENTIALS_ID = 'jfrog-creds'
        IMAGE_PREFIX = 'docker-assignment' // change this to your project or team name
    }

    stages {
        stage('Clone, Build and Push Images') {
            steps {
                script {
                    def images = [
                        [name: 'maven-image', dockerfile: 'Dockerfile.maven'],
                        [name: 'git-image', dockerfile: 'Dockerfile.git'],
                        [name: 'tomcat-image', dockerfile: 'Dockerfile.tomcat']
                    ]

                    docker.withRegistry("https://${REGISTRY}", CREDENTIALS_ID) {
                        for (img in images) {
                            def imageName = "${REGISTRY}/${IMAGE_NAMESPACE}/${img.name}:latest"
                            echo " Building: ${imageName}"
                            def builtImage = docker.build(imageName, "-f ${img.dockerfile} .")
                            echo " Pushing: ${imageName}"
                            builtImage.push()
                            
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            echo "✅ All images built and pushed successfully!"
        }
        failure {
            echo "❌ Something went wrong during build or push."
        }
    }
}