def registry = "https://trialjnpes7.jfrog.io/"

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
        
        stage('Jar Publish') {
            steps {
               script {
                  echo "------------ Jar Publish Started ----------------"
                  def server = Artifactory.newServer url: registry + "/artifactory", credentialsID: "artifact-cred"
                  def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}"
                  def uploadSpec = """{
                        "files": [
                           {
                            "pattern": "jarstaging/(*)",
                            "target": "zy-libs-release-local/{1}",
                            "flat": "false",
                            "props": "${properties}"
                            "exclusions: [ "*.sha1", "*.md5"]"
                           }
                        ]
                  }"""
                 def buildInfo = server.upload(uploadSpec)
                 buildInfo.env.collect()
                 server.publishBuildInfo(buildInfo)
                 echo "------------ Jar Publish Completed ---------------"
               }
            }
        }

    }
}
