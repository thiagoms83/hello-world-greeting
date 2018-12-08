
node('docker') {
  env.JAVA_HOME="${tool 'java_slave'}"
  stage('Poll') { 
    checkout scm
  } 
  stage('Build & Unit test') {
    sh 'echo $JAVA_HOME'
    sh 'mvn clean verify -DskipITs=true'; 
    junit '**/target/surefire-reports/TEST-*.xml' 
    archive 'target/*.jar' 
  } 
  stage(' Static Code Analysis') {
    sh 'mvn clean verify sonar:sonar -Dsonar.host.url=http://172.17.0.1:9000 -Dsonar.login=881dbcfad6da09296c2bf24b6c2c30e0311c8a7b -Dsonar.projectName=example-project -Dsonar.projectKey=example-project -Dsonar.projectVersion=$BUILD_NUMBER'; 
  } 
  stage (' Integration Test') { 
    sh 'mvn clean verify -Dsurefire.skip=true'; 
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
  node ('production') { 
stage ('Deploy to Prod') { 
sh 'wget http://localhost:8081/nexus/content/repositories/releases/org/jenkins-ci/main/hello/0.0.1/hello-0.0.1.war'
}
}
