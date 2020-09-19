pipeline {
    agent any
    environment{
        COMPOSE_FILE = '/var/weissbeerger-docker/docker-compose.yml'
        BUILD_TAG_NGINX = "${BUILD_NUMBER}"
    }
    stages{
        stage('stop existing container'){
            steps{
                sh 'docker-compose stop nginx'
            }
        }
        stage('remove stopped container'){
            steps{
                sh 'docker-compose rm -f'
            }
        }
        stage('build and start new nginx container'){
            steps{
                sh 'docker-compose up -d --force-recreate --build nginx'
            }
        }
    }
}
