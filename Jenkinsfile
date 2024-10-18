pipeline {

    agent any

    tools {
        jdk 'jdk17'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Replace Application Properties') {
            steps {
                sh 'chmod -R u+w ./src/main/resources'
                withCredentials([file(credentialsId: 'config-service-application-yml', variable: 'application')]) {
                    script {
                        sh 'cp ${application} ./src/main/resources/application.yml'
                    }
                }
            }
        }

        stage('Build & Test') {
            steps {
                sh './gradlew clean build --no-daemon'
            }
        }


        stage('Build & Tag Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerhub-credential-alphaka') {
                        sh 'docker build -t alphaka/config-service:latest .'

                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-credential') {
                        sh 'docker push alphaka/config-service:latest'
                    }
                }
            }
        }

    }
}