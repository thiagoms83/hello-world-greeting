node('docker') { 
  stage('Poll') { 
    checkout scm
  } 
  stage('Build & Unit test') { 
    sh 'mvn clean verify -DskipITs = true'; 
    junit '**/target/surefire-reports/TEST-*.xml' 
    archive 'target/*.jar' 
  } 
  stage(' Static Code Analysis') {
    sh 'mvn clean verify sonar:sonar -Dsonar.projectName = example-project -Dsonar.projectKey = example-project -Dsonar.projectVersion = $ BUILD_NUMBER'; 
  } 
  stage (' Integration Test') { 
    sh 'mvn clean verify -Dsurefire.skip = true'; 
    junit '**/target/failsafe-reports/TEST-*.xml' 
    archive 'target/*.jar' 
  } 
  stage('Publish') {
     nexusPublisher nexusInstanceId: 'nexus-server', nexusRepositoryId: 'releases', 
       packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', 
                                                             filePath: 'target/hello-0.0.1.war']], 
                   mavenCoordinate: [artifactId: 'hello', groupId: 'org.jenkins-ci.main', 
                                     packaging: 'war', version: '0.0.1']]]
   }
}
