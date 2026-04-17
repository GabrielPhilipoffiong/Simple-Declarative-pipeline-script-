pipeline {
    agent any

    tools {
        maven 'maven3'
    }

    environment {
        IMAGE_NAME = "yourdockerhub/my-app:latest"
    }

    stages {

        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://your-repo-url.git'
            }
        }

        stage('Build & Test') {
            steps {
                sh "mvn clean package"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube-server') {
                    sh "mvn sonar:sonar"
                }
            }
        }

        stage('Deploy to Artifactory') {
            steps {
                sh "mvn deploy"
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                sh "docker build -t $IMAGE_NAME ."
                sh "docker push $IMAGE_NAME"
            }
        }

        stage('Deploy Container') {
            steps {
                sh "docker rm -f mango-cont || true"
                sh "docker run -d -p 8070:8080 --name mango-cont $IMAGE_NAME"
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed'
        }
        success {
            echo 'Deployment successful'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}
