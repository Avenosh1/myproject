pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/<username>/<repo-name>.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t java-app .'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    sh 'docker run java-app'
                }
            }
        }
    }
}
