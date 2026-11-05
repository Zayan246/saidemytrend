pipeline {
    agent any
    environment {
          PATH = "/opt/maven/bin:$PATH"
    }

    stages {
        stage('build') {
             steps {
                sh 'mvn clean deploy -Dmaven.test.skip=true'
              }
        }
        
        stage('test') {
             steps {
                echo "--------------- unit test started ------------------"
                sh 'mvn surefire-report:report'
                echo "--------------- unit test completed ----------------"
             }
        }

        stage('SonarQube Analysis') {
             environment {
                scannerHome = tool 'saidemy-sonar-scanner'
             }

             steps {
                withSonarQubeEnv('saidemy-sonarqube-server') {
                      sh '${scannerHome}/bin/sonar-scanner'
                } 
             } 
        }
    }
}
