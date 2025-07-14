pipeline {
    agent any

    environment {
        REGISTRY = "trialfwrmrd.jfrog.io"                 // JFrog domain
        IMAGE_NAMESPACE = "docker-assignment"             // Logical repo name
        CREDENTIALS_ID = 'jfrog-creds'                    // Your Jenkins credentials ID
    }

    stages {
        stage('Clone, Build & Push Docker Images') {
            steps {

                script {
                    def images = [
                        [name: 'maven', dockerfile: 'Dockerfile.maven'],
                        [name: 'git', dockerfile: 'Dockerfile.git'],
                        [name: 'tomcat', dockerfile: 'Dockerfile.tomcat']
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
            echo " All images pushed to JFrog Artifactory successfully."
        }
        failure {
            echo " One or more builds failed. Please check the logs."
        }
    }
}