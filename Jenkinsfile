pipeline {
    agent any
    stages {
        stage ('Clone') {
            steps {
                git branch: 'master', url: "https://github.com/midhunthampi/WebApp.git"
            }
        }
        
        stage('Sonarqube') {
           steps {
                 withSonarQubeEnv(credentialsId: 'sonarqube1', installationName: 'sonarqube1')  {
                 echo "Sonar Qube Code Analysis Completed"
                 }
           }
         }
        
		stage('compile') {
           steps {
                 echo "Compile Complete"
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
            }
            post {
                 always {
                     jiraSendBuildInfo site: 'devopsgroups2.atlassian.net', branch: 'DEV-6 Implement Code'
                 }
             }
        }
		
	stage('deploy to QA ') {
           steps {
		   		   
                 deploy adapters: [tomcat7(credentialsId: 'tomcat', path: '', url: 'http://18.191.223.34:8080/')], contextPath: '/QAWebapp', war: '**/*.war'
           }
         }
		 
		 
		stage('Artifactory Deployment') {
           steps {
                 echo "Completed Artifactory Deployment"
           }
         }
		 
		 
		 stage('Functional Testing') {
           steps {
                 echo "Functional Testing Completed"
           }
         }
		 
		 
		stage('Performance Testing') {
           steps {
                 echo "Performace Testing Completed with Blaze Meter"
           }
         }
		 
		 stage('Deploy to Production ') {
		    when {
                branch 'production'
            }
           steps {
                 echo "Deployment on Production is compelted"
           }
         }
		 
		 
		 stage('Sanity on Deployment ') {
		    when {
                branch 'production'
            }
           steps {
                 echo "Sanity Successfully Completed on Deployment"
           }
		   
		   post {
                 always {
                     jiraSendBuildInfo site: 'devopsgroups2.atlassian.net', branch: 'DEV-6 Implement Code'
                 }
             }
         }
		
		
		
    }
}
