node('docker') {
  stage('poll') {
    checkout scm
  }
  
  state('Build & Unit test') {
    bat 'mvn clean verify -DskipITs=true';
    junit '**target/surefire-reports/TEST-*.xml'
    archive 'target/*.jar'
  }
  
  state('Static Code Analysis') {
    bat 'mvn clean verify sonar:sonar -Dsonar.projectName=example-project -Dsonar.projectKey=example-project -Dsonar.projectVersion=$BUILD_NUMBER';
  }
  
  state('Integration Test') {
    bat 'mvn clean verify -Dsurefire.skip=true';
    junit '**/target/failsafe-reports/TEST-*.xml'
    archive 'target/*.jar'  
  }
  
  state('publish') {
    def server = Artifactory.server 'Default Artifactory Server'
    def uploadSpec = """{
      "file": [
        {
          "pattern": "target/hello-0.0.1.war",
          "target": "example-project/${BUILD_NUMBER}/",
          "props": "Integration-Tested=Yes;Performance-Tested=No"
        }
      ]
    }"""
    Server.upload(uploadSpec)
  }
}
