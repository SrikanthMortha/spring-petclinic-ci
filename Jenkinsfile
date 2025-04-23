pipeline {
    agent any

    environment {
        SONARQUBE_SCANNER = 'SonarQube Scanner' // Name from Jenkins Global Tool Config
        DOCKER_IMAGE = "srikanthmortha/spring-petclinic-ci:${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'git@github.com:SrikanthMortha/spring-petclinic-ci.git'
            }
        }

        stage('Code Quality - SonarQube') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn clean verify sonar:sonar'
                }
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}")
                }
            }
        }

        stage('Trivy Scan') {
            steps {
                sh "trivy image ${DOCKER_IMAGE} || true"
            }
        }

        stage('Docker Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials-id') {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }
    }
}

