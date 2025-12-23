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
    withCredentials([usernamePassword(credentialsId: 'tomcat-manager-creds',
                                      usernameVariable: 'TM_USER',
                                      passwordVariable: 'TM_PASS')]) {
      sh '''
        set -e

        # Find the first WAR produced by the build
        WAR=$(ls -1 target/*.war 2>/dev/null | head -n 1 || true)
        if [ -z "$WAR" ]; then
          echo "WAR not found in target/. Did the build succeed and is packaging=war?" >&2
          ls -la target || true
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
