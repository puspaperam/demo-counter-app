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
                sh 'mvn mvn clean install'
            }
        }
    }
}