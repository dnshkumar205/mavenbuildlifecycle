node {
   def mvnHome
   stage('Checkout') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/shekharshamra/jenkin'
      // Get the Maven tool.
      // ** NOTE: This 'M3' Maven tool must be configured
      // **       in the global configuration.           
      mvnHome = tool 'M3'
   }
   stage('Build') {
      // Run the maven build
      if (isUnix()) {
         sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean install"
      } else {
         bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore clean install/)
      }
   }
   stage('Unit Testing') {
      junit 'in28minutes-core/target/surefire-reports/*.xml'
   }
  
   stage('SonarQube analysis') { 
        withSonarQubeEnv('sonar') { 
          
        sh "mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.2:sonar -f ${WORKSPACE}/pom.xml -Dsonar.host.url=${env.SONAR_HOST_URL} -Dsonar.login=${env.SONAR_AUTH_TOKEN}  -Dsonar.projectKey=test-jenkins-sonar -Dsonar.projectName=test-jenkins-sonar -Dsonar.sources=src -Dsonar.language=java -Dsonar.test.inclusions=**/*Test*/** -Dsonar.exclusions=**/*Test*/**"
          
          
        }
    }

    stage('Publish') {
     nexusPublisher nexusInstanceId: 'MyNexusRepo', nexusRepositoryId: 'jenkinspublisher', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'in28minutes-web-common/target/in28minutes-web-common-1.0-SNAPSHOT.jar']], mavenCoordinate: [artifactId: 'jenkins-war', groupId: 'org.jenkins-ci.main', packaging: 'jar', version: '2.23']]]
   }
}
