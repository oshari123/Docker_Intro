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

                    withCredentials([usernamePassword(credentialsId: "${JFROG_CREDENTIALS_ID}", passwordVariable: 'JFROG_PASS', usernameVariable: 'JFROG_USER')]) {

                        images.each { img ->
                            def imageTag = "${JFROG_DOCKER_REGISTRY}/${IMAGE_PREFIX}/${img.name}:latest"

                            echo "🔧 Building ${img.name} using ${img.dockerfile}"
                            def image = docker.build(imageTag, "-f ${img.dockerfile} .")

                            echo "🚀 Pushing image: ${imageTag}"
                            sh "docker login ${JFROG_DOCKER_REGISTRY} -u ${JFROG_USER} -p ${JFROG_PASS}"
                            image.push()
                            sh "docker logout ${JFROG_DOCKER_REGISTRY}"
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