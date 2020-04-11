pipeline {
    agent any
    stages {
        stage ('Clone') {
            steps {
                git branch: 'master', url: "https://github.com/midhunthampi/WebApp.git"
		slackSend (color: '#FFFF00', message: "CLONED SUCCESSFULLY: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
            }
        }
        
        stage('Sonarqube') {
           steps {
                 withSonarQubeEnv(credentialsId: 'sonarqube1', installationName: 'sonarqube1')  {
                 echo "Sonar Qube Code Analysis Completed"
	         slackSend (color: '#FFFF00', message: "SONAR QUBE CODE ANALYSIS COMPLETED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                 }
           }
         }	 
        stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: "thiyaguartifactory",
                    url: "https://thiyagumaniserver.jfrog.io/thiyagumaniserver",
                    username: 'admin',
                    password: 'Qt6Mh2Sh6Hc2Ai'
                )

                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "thiyaguartifactory",
                    releaseRepo: "libs-release-local",
                    snapshotRepo: "libs-snapshot-local"
                )

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "thiyaguartifactory",
                    releaseRepo: "libs-release",
                    snapshotRepo: "libs-snapshot"
                )
		slackSend (color: '#FFFF00', message: "ARTIFACTORY CONFIGURED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
            }
        }

        stage ('Exec Maven') {
            steps {
                rtMavenRun (
                    tool: "maven", // Tool name from Jenkins configuration
                    pom: 'pom.xml',
                    goals: 'clean install -U',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
            }
        }

        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "thiyaguartifactory"
                )
		slackSend (color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
		echo '${env.JOB_NAME} [${env.BUILD_NUMBER}]'
            }
            post {
                 always {
                     jiraSendBuildInfo site: 'devopsgroups2.atlassian.net', branch: 'DEV-6 Implement Code'
		     slackSend (color: '#FFFF00', message: "BUILD COMPLETED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                 }
             }
        }
		
	stage('deploy to QA ') {
           steps {
		 deploy adapters: [tomcat7(credentialsId: 'tomcat', path: '', url: 'http://18.191.223.34:8080/')], contextPath: '/QAWebapp', war: '**/*.war'
		   slackSend (color: '#FFFF00', message: "DEPLOYED TO QA: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
           }
         }	 
		 
	stage('Functional Testing') {
           steps {
                 publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '\\functionaltest\\target\\surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: ''])
		 slackSend (color: '#FFFF00', message: "FUNCTIONAL TESTING COMPLETED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
           }
         }
		 
		 
		stage('Performance Testing') {
           steps {
                  blazeMeterTest credentialsId: 'Blazemeter', testId: '7910253.taurus', workspaceId: '472855'
		   slackSend (color: '#FFFF00', message: "PERFORMANCE TESTING COMPLETED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
           }
         }
		 
		 stage('Deploy to Production ') {
           steps {
          deploy adapters: [tomcat7(credentialsId: 'tomcat', path: '', url: 'http://18.219.155.56:8080/')], contextPath: '/ProdWebapp', war: '**/*.war'
          slackSend (color: '#FFFF00', message: "DEPLOYED TO PRODUCTION: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
           }
         }
		 
		 
		 stage('Sanity on Deployment ') {
           steps {
                 publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '\\Acceptancetest\\target\\surefire-reports\\', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: ''])
	         slackSend (color: '#FFFF00', message: "SANITY ON DEPLOYMENT COMPLETED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
           }
		   
		   post {
                 always {
			jiraSendBuildInfo site: 'devopsgroups2.atlassian.net', branch: 'DEV-6 Implement Code' 
			 slackSend (color: '#FFFF00', message: "COMPLETED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                 }
             }
         }
		
		
		
    }
}
