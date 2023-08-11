pipeline {
    agent any

    environment {
        NGINX_PORT = 9000
        JENKINS_PORT = 49000
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    docker.image('node:16-buster-slim').withRun("-p ${env.NGINX_PORT}:${env.NGINX_PORT}") { container ->
                        sh 'npm install'
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    docker.image('node:16-buster-slim').inside("-p ${env.NGINX_PORT}:${env.NGINX_PORT}") {
                        sh './jenkins/scripts/test.sh'
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                docker.image('nginx').withRun("-p ${env.NGINX_PORT}:${env.NGINX_PORT}") { container ->
                    sh """
                        echo 'server {
                            listen       9000;
                            server_name  localhost;

                            location / {
                                proxy_pass http://localhost:${env.JENKINS_PORT};
                            }
                        }' > nginx.conf

                        mv nginx.conf /etc/nginx/conf.d/default.conf

                        nginx -g 'daemon off;'
                    """
                }
            }
        }
    }
}
