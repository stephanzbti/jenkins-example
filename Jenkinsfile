node {
    def frontend
    def backend
    stage('build') {
        echo "Starting Build"
        frontend = docker.build("stephanzbti/frontend-smarttbot")
        backend = docker.build("stephanzbti/backend-smarttbot")
        echo "Finishing Build"
    }
    stage('RepositoryImage') {
        echo "Starting Login Repository"
        docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
            echo "Starting Push Image"
            frontend.push("${env.BUILD_NUMBER}")
            frontend.push("latest")
            backend.push("${env.BUILD_NUMBER}")
            backend.push("latest")
            echo "Finishing Push Image"
        }
        echo "Finishing Login Repository"
    }
}