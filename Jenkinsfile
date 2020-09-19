pipeline {
    agent any
    environment{
        COMPOSE_FILE = '/var/devops/docker-compose.yml'
        BUILD_TAG_NGINX = "${BUILD_NUMBER}"
    }
    stages{
        stage('start'){
            steps{
                sh 'docker-compose up -d --force-recreate --build nginx'
            }
        }
    }
}
