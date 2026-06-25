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
                    repository: 'deployapp-release', 
                    version: '1.0.0'
                }
            }
        }
    }
}