pipeline {
    agent any
    
    tools {
        //jdk 'jdk11'
        maven 'maven3'
    }
    environment {
        TOMCAT_HOST = '3.25.101.121'
        TOMCAT_PORT = '8082'
        APP_PATH    = '/spring-boot-demo'

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
         stage('Deploy to Tomcat') {
      steps {
        // Add Jenkins credentials (Kind: Username with password)
        // ID must be 'tomcat-manager-creds' or change below accordingly.
        withCredentials([usernamePassword(credentialsId: 'tomcat-manager-creds',
                                          usernameVariable: 'TM_USER',
                                          passwordVariable: 'TM_PASS')]) {
          sh '''
            set -e
            WAR=target/*.war
            if [ ! -f $WAR ]; then
              echo "WAR not found. Build step failed?" >&2
              exit 2
            fi

            echo "Deploying $WAR to http://${TOMCAT_HOST}:${TOMCAT_PORT}${APP_PATH} ..."
            curl --fail -u "$TM_USER:$TM_PASS" -T "$WAR" \
              "http://${TOMCAT_HOST}:${TOMCAT_PORT}/manager/text/deploy?path=${APP_PATH}&update=true"

            echo "✅ Deploy OK: http://${TOMCAT_HOST}:${TOMCAT_PORT}${APP_PATH}"
          '''
        }
      }
    }
  }

  post {
    success {
      echo "Build & deploy succeeded."
    }
    failure {
      echo "Build or deploy failed."
    }
    always {
      archiveArtifacts artifacts: 'target/*.war', fingerprint: true
    }
        
    }
}
