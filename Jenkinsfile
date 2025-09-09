pipeline {
    agent any

    environment {
        APP_NAME = "my-app"
        NEXUS_CREDENTIALS_ID = 'nexus-credentials'
        MAVEN_TOOL = 'M3'
        DOCKER_IMAGE = 'my-app-image'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/tribhuwanpandey/Simple-maven-project.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Upload Artifact to Nexus') {
            steps {
                withMaven(maven: "${MAVEN_TOOL}") {
                    sh "mvn deploy -s /var/lib/jenkins/.m2/settings.xml -DskipTests"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${DOCKER_IMAGE}:latest .'
            }
        }


    }

    post {
        always {
            cleanWs()
        }
    }
}
