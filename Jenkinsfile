pipeline{
    agent any
    stages{
        stage('code checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/puspaperam/demo-counter-app.git'
            }
        }
    }
}