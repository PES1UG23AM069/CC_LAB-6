pipeline {
    agent any

    stages {

        stage('Create Network') {
            steps {
                sh 'docker network create labnet || true'
            }
        }

        stage('Build Backend Image') {
            steps {
                sh 'docker build -t backend-app backend'
            }
        }

        stage('Deploy Backends') {
            steps {
                sh '''
                docker rm -f backend1 backend2 || true
                docker run -d --name backend1 --network labnet backend-app
                docker run -d --name backend2 --network labnet backend-app
                sleep 5
                '''
            }
        }

        stage('Start Nginx') {
            steps {
                sh '''
                docker rm -f nginx-lb || true
                docker run -d --name nginx-lb -p 80:80 --network labnet nginx
                sleep 5
                docker cp nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf
                sleep 3
                docker exec nginx-lb nginx -s reload
                '''
            }
        }
    }
}

