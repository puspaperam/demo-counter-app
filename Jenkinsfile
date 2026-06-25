pipeline{
    agent any
    stages{
        stage('code checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/puspaperam/demo-counter-app.git'
            }
        }
        stage('unit testing'){
            steps{
                sh 'mvn test'
            }
        }
        stage('inntegration testing'){
            steps{
                sh 'mvn verify -DskipUnitTests'
            }
        }
    }
}