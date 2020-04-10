      pipeline {
        agent none
        tools {
          maven 'maven'
        }
        stages {
		
		  // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
          def server = Artifactory.server "artifactory"
          // Create an Artifactory Maven instance.
          def rtMaven = Artifactory.newMavenBuild()
          def buildInfo
    
          rtMaven.tool = "maven"
 
 
		  stage('Clone sources') {
                git url: 'https://github.com/midhunthampi/WebApp.git'
          }
		  
          stage("build & SonarQube analysis") {
            agent any
            steps {
              withSonarQubeEnv('sonar') {
                sh 'mvn clean package sonar:sonar -Dsonar.host.url=http://sonar-devops.westus.cloudapp.azure.com/ -Dsonar.login=a9c8113177c7cfd5d1fa326e88261b0e00f210d1 -Dsonar.sources=. -Dsonar.tests=. -Dsonar.test.inclusions=**/test/java/servlet/createpage_junit.java -Dsonar.exclusions=**/test/java/servlet/createpage_junit.java'
              }
            }
			post {
                 always {
                     jiraSendBuildInfo site: 'devopsgroups2.atlassian.net', branch: 'DEV-6 Implement Code'
                 }
             }
          }
		  
		  stage('Artifactory configuration') {
            // Tool name from Jenkins configuration
            rtMaven.tool = "maven"
            // Set Artifactory repositories for dependencies resolution and artifacts deployment.
            rtMaven.deployer releaseRepo:'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
            rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server
          }

          stage('Maven build') {
            buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean install'
          }
		  
          stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
          }
        }
      }
	  
	  
