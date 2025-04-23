pipeline {
    agent any

    environment {
        SONARQUBE = credentials('sonar-token') // already working
        DOCKERHUB = credentials('dockerhub')   // just added
        IMAGE_NAME = "srikanthmortha/spring-petclinic"
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'git@github.com:SrikanthMortha/spring-petclinic-ci.git', branch: 'main'
            }
        }

        stage('Code Quality - SonarQube') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=spring-petclinic -Dsonar.host.url=http://localhost:9000 -Dsonar.login=$SONARQUBE'
                }
            }
        }

        stage('Build JAR') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub', url: '']) {
                    sh 'docker push $IMAGE_NAME:latest'
                }
            }
        }
    }

    post {
        success {
            echo 'CI Pipeline Completed Successfully 🚀'
        }
        failure {
            echo 'CI Pipeline Failed ❌'
        }
    }
}

