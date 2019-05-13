node {
    def frontend
    def backend

    environment {
        ALLOWED_ORIGIN = '*'
        REDIS_ADDR     = 'redis:6379'
        ENVIRONMENT    = 'production'
        REACT_APP_BACKEND_WS     = 'ws://localhost:8080'
        REACT_APP_BACKEND_URL    = 'http://localhost:8080'
        NODE_ENV       = 'production'
    }
    stage('build') {
        echo "Starting Build"
        frontend = docker.build("stephanzbti/frontend-smarttbot", "./frontend")
        backend = docker.build("stephanzbti/backend-smarttbot", "./backend")
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