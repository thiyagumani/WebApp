pipeline {
    agent any
    stages {
        stage ('Clone') {
            steps {
                git branch: 'master', url: "https://github.com/midhunthampi/WebApp.git"
            }
		 post {
                 success {
		     slackSend (color: '#FFFF00', message: "Clone Successful: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                 }
	         failure {
		     slackSend (color: '#FFFF00', message: "Clone Failed: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                 }   
             }
        }
        
        stage('Sonarqube') {
           steps {
                // withSonarQubeEnv(credentialsId: 'sonarqube1', installationName: 'sonarqube1')  {
                 echo "Sonar Qube Code Analysis Completed"
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
            }
		 post {
                 success {
		     slackSend (color: '#FFFF00', message: "Artifactory Configured Successfully: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                 }
	         failure {
		     slackSend (color: '#FFFF00', message: "Artifactory Configuraion Failed: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                 }   
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
                     jiraSendBuildInfo site: 'devopsgroups2.atlassian.net', branch: 'TEL-1 Creating Service Request for wireless and wireline service issues'
                 }
                 success {
		     slackSend (color: '#FFFF00', message: "Build Successful: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                 }
	         failure {
		     slackSend (color: '#FFFF00', message: "Build Failed: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                 } 
             }
        }
		
	stage('deploy to QA ') {
           steps {
		 deploy adapters: [tomcat7(credentialsId: 'tomcat', path: '', url: 'http://18.191.223.34:8080/')], contextPath: '/QAWebapp', war: '**/*.war'
            }
		 post {
                 success {
		     slackSend (color: '#FFFF00', message: "QA Deployment Successful: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                 }
	         failure {
		     slackSend (color: '#FFFF00', message: "QA Deployment Failed: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                 }   
             }
         }	 
		 
	stage('Functional Testing') {
           steps {
                 publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '\\functionaltest\\target\\surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: ''])
            }
		 post {
                 success {
		     slackSend (color: '#FFFF00', message: "Functional Testing Completed Successful: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                 }
	         failure {
		     slackSend (color: '#FFFF00', message: "Functional Testing Failed: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                 }   
             }
         }
		 
		 
		stage('Performance Testing') {
           steps {
                  blazeMeterTest credentialsId: 'BlazeMeterNew', testId: '7912478.taurus', workspaceId: '472836'
		  }
		post {
                 success {
		     slackSend (color: '#FFFF00', message: "Performance Testing Completed: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                 }
	         failure {
		     slackSend (color: '#FFFF00', message: "Performance Testing Failed: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                 }   
             }
         }
		 
		 stage('Deploy to Production ') {
           steps {
          deploy adapters: [tomcat7(credentialsId: 'tomcat', path: '', url: 'http://18.219.155.56:8080/')], contextPath: '/ProdWebapp', war: '**/*.war'
            }
		 post {
                 success {
		     slackSend (color: '#FFFF00', message: "Deployment to Production  Successful: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                 }
	         failure {
		     slackSend (color: '#FFFF00', message: "Deployment to Production Failed: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                 }   
             }
         }
		 
		 
	stage('Sanity on Deployment') {
           steps {
                 publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '\\Acceptancetest\\target\\surefire-reports\\', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: ''])
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
