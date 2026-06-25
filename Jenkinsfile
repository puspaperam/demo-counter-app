pipeline {
    agent {
        label 'docker-agent'
    }

    stages {

        stage('Code Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/puspaperam/demo-counter-app.git'
            }
        }

        stage('Unit Testing') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Integration Testing') {
            steps {
                sh 'mvn verify -DskipUnitTests'
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Static Code Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh 'mvn clean package sonar:sonar'
                }
            }
        }

        stage('Quality Gate Status') {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Upload Jar File To Nexus') {
            steps {
                script {

                    def readPomVersion = readMavenPom file: 'pom.xml'

                    def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ?
                                    "deployapp-snapshot" :
                                    "deployapp-release"

                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: '192.168.124.129:8081',
                        groupId: 'com.example',
                        version: readPomVersion.version,
                        repository: nexusRepo,
                        credentialsId: 'nexus_cre',
                        artifacts: [
                            [
                                artifactId: 'springboot',
                                classifier: '',
                                file: 'target/Uber.jar',
                                type: 'jar'
                            ]
                        ]
                    )
                }
            }
        }

        stage('Docker Image Build') {
            steps {
                sh """
                    docker build -t ${JOB_NAME}:v1.${BUILD_ID} .
                    docker tag ${JOB_NAME}:v1.${BUILD_ID} puspaperam/${JOB_NAME}:v1.${BUILD_ID}
                    docker tag ${JOB_NAME}:v1.${BUILD_ID} puspaperam/${JOB_NAME}:latest
                """
            }
        }
    }
}