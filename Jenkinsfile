pipeline {
    agent  {
        docker {
            image maven:3.9.6-eclipse-temurin-17
            args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    environment {
        DOCKER_IMAGE = "yuv1991/java-app:${BUILD_NUMBER}"
        REGISTRY_CREDENTIALS = credentials('docker-credentials')
    }
    stages {
        stage ('GIT Checkout Stage') {
            steps {
                sh 'git clone https://github.com/yuvaraj-1991/mvn-java-docker.git'
                sh 'echo "git check out successful"'
            }            
        }   
        stage ('Build the Package') {
            steps {
                sh 'cd mvn-java-docker'
                sh 'mvn clean package'
            }            
        } 
        stage ('Static Code Analysis stage-03') {
            environment {
                SONAR_URL = 'http://172.17.0.2:9000'
            }
            steps {
                    withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_AUTH_TOKEN')]) {
                    sh "cd mvn-java-docker"    
                    sh "mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}"
                }
            }
        }   
        stage ('Build and Push the Docker Image') {
            steps {
                sh '''
                    cd mvn-java-docker && docker build -t ${DOCKER_IMAGE} .
                    docker login -u ${env.REGISTRY_CREDENTIALS.USR} -p ${env.REGISTRY_CREDENTIALS.PSW}
                    docker push ${DOCKER_IMAGE}
                '''
            }            
        }             
    }
}