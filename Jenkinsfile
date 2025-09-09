pipeline {
    agent any

    environment {
        NEXUS_URL = 'http://nexus.example.com/repository/maven-releases/'
        NEXUS_CREDENTIALS_ID = 'nexus-credentials'
        DOCKER_IMAGE = 'my-app-image'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/your-repo/my-app.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Push Artifact to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: env.NEXUS_CREDENTIALS_ID, usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                    sh '''
                    mvn deploy:deploy-file \
                        -Durl=${NEXUS_URL} \
                        -DrepositoryId=nexus-releases \
                        -DgroupId=com.example \
                        -DartifactId=my-app \
                        -Dversion=1.0-SNAPSHOT \
                        -Dpackaging=jar \
                        -Dfile=target/my-app-1.0-SNAPSHOT.jar
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${DOCKER_IMAGE}:latest .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker tag ${DOCKER_IMAGE}:latest your-docker-repo/${DOCKER_IMAGE}:latest
                    docker push your-docker-repo/${DOCKER_IMAGE}:latest
                    '''
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}