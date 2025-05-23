pipeline {
    agent  {
        docker {
            image 'maven:3.9.6-eclipse-temurin-17'
            args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    environment {
        DOCKER_IMAGE = "yuv1991/java-app:${BUILD_NUMBER}"
        REGISTRY_CREDENTIALS = credentials('docker-credentials-lts')
    }
    stages {
        stage('Install Docker CLI') {
            steps {
                sh '''
                    apt-get update
                    apt-get install -y docker.io
                '''
            }
        }
        stage ('GIT Checkout Stage') {
            steps {
              checkout scm 
            }            
        }   
        stage ('Build the Package') {
            steps {
                sh 'mvn clean package'                
            }            
        } 
        stage ('Static Code Analysis stage-03') {
            environment {
                SONAR_URL = 'http://172.17.0.2:9000'
            }
            steps {
                    withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_AUTH_TOKEN')]) {
                    sh "mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}"
                }
            }
        }   
        stage ('Build and Push the Docker Image') {
            steps {
                // dir('mvn-java-docker')
                sh """
                    docker build -t ${DOCKER_IMAGE} .
                    docker login -u ${env.REGISTRY_CREDENTIALS_USR} -p ${env.REGISTRY_CREDENTIALS_PSW}
                    docker push ${DOCKER_IMAGE}
                """
            }            
        }             
    }
}
