pipeline{
    agent any
    stages{
        stage('Code Checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/puspaperam/demo-counter-app.git'
            }
        }
        stage('Unit Testing'){
            steps{
                sh 'mvn test'
            }
        }
        stage('Integration Testing'){
            steps{
                sh 'mvn verify -DskipUnitTests'
            }
        }
        stage('Maven Build'){
            steps{
                sh 'mvn clean install'
            }
        }
        stage('Static Code Analysis'){
            steps{
                withSonarQubeEnv(credentialsId: 'sonar_app', installationName: 'sonarqube') {
                sh 'mvn clean package sonar:sonar'
            }
          }
        }
        stage('Quality Gate Status'){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar_app'
                }
            }
        }
        stage('Upload War File To Nexus'){
            steps{
                script{
                    def readPomVersion=readMavenPom file:'pom.xml'
                    def nexusRepo=readPomVersion.version.endsWith("SNAPSHOT") ? "deployapp-snapshot" : "deployapp-release"
                    nexusArtifactUploader artifacts:
                    [
                        [artifactId: 'springboot', 
                        classifier: '', file: 'target/Uber.jar',
                        type: 'jar'
                        ]
                    ], 
                    credentialsId: 'nexus_cre', 
                    groupId: 'com.example', 
                    nexusUrl: '192.168.124.129:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: nexusRepo, 
                    version: "${readPomVersion.version}"
                }
            }
        }
        stage('Docker Image Build'){
    steps{
        script{
            sh """
                docker build -t ${JOB_NAME}:v1.${BUILD_ID} .
                docker tag ${JOB_NAME}:v1.${BUILD_ID} puspaperam/${JOB_NAME}:v1.${BUILD_ID}
                docker tag ${JOB_NAME}:v1.${BUILD_ID} puspaperam/${JOB_NAME}:latest
            """
                }
            }
        }
    }
}