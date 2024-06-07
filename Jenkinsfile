pipeline {
    agent any
    
    tools {
        // Define the Maven tool to use
        maven 'maven-3.8.4'
    }
    
    stages {
        stage('SCM Checkout') {
            steps {
                // Checkout the source code from the specified Git repository
                git branch: 'master', url: 'https://github.com/Kumar-arj/microservice-demo-use'
            }
        }
        
        stage('Maven Build') {
            steps {
                // Run Maven build
                sh 'mvn clean install'
            }
        }
    }
}
