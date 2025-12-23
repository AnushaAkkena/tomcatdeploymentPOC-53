pipeline {
    agent any
    
    tools {
        //jdk 'jdk11'
        maven 'maven3'
    }
    stages {
        stage('Git Checkout ') {
            steps {
                git url: 'https://github.com/AnushaAkkena/tomcatdeploymentPOC-53.git', branch: 'main'
            }
        }
        
        stage('Build') {
            steps {
                    sh "mvn clean package"
            }
        }
        
        stage('Run Test Cases') {
            steps {
                    sh "mvn test"
            }
        }
        
    }
}
