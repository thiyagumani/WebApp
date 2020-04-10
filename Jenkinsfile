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
                 withSonarQubeEnv('sonarqube1') {
                 mvn ‘${SONAR_MAVEN_GOAL} -Dsonar.host.url=${SONAR_HOST_URL} -Dsonar.login=${SONAR_AUTH_TOKEN} ${SONAR_EXTRA_PROPS} ‘
                }
              timeout(time: 10, unit: 'MINUTES') {
              waitForQualityGate abortPipeline: true
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

        stage ('Xray scan') {
            steps {
                xrayScan (
                    serverId: "thiyaguartifactory",
                    failBuild: false
                )
            }
        }
    }
}
