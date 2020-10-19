pipeline {
    agent any
    environment{
        COMPOSE_FILE = '/var/jenkins-docker/docker-compose.yml'
        BUILD_TAG_NGINX = "${BUILD_NUMBER}"
    }
    stages{
        stage('build and start new nginx container'){
            steps{
                sh 'docker-compose up -d --force-recreate --build nginx'
            }
        }
    }
}
