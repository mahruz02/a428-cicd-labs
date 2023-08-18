pipeline {
    agent {
        docker {
            image 'timbru31/node-alpine-git:16' 
            args '-p 3000:3000' 
        }
    }
    environment {
        PUBLIC_URL       = 'https://mahruz02.github.io/a428-cicd-labs'
        GITHUB_TOKEN     = credentials('jenkins-github-token')
        GITHUB_REPOSITORY = 'mahruz02/a428-cicd-labs'
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install' 
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Manual Approval') {
            steps {
                input message: 'Lanjutkan ke tahap Deploy?'
            }
        }
        stage('Deploy') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                sh 'chmod +x ./jenkins/scripts/github-pages.sh && ./jenkins/scripts/github-pages.sh'
                sleep time: 60, unit: 'SECONDS' // Adding a 1-minute delay
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'build/**/*', fingerprint: true
        }
    }
}
