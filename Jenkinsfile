node {
    // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
    def server = Artifactory.server "demoartifactory"
    // Create an Artifactory Maven instance.
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo
    
 rtMaven.tool = "maven"
    stages {
    stage('Clone sources') {
        steps {
        git url: 'https://github.com/midhunthampi/WebApp.git'
        }
    }

    stage('Artifactory configuration') {
        steps {
        // Tool name from Jenkins configuration
        rtMaven.tool = "maven"
        // Set Artifactory repositories for dependencies resolution and artifacts deployment.
        rtMaven.deployer releaseRepo:'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
        rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server
        }
    }

    stage('Maven build') {
        steps {
        buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean install'
        }
    }

    stage('Publish build info') {
        server.publishBuildInfo buildInfo
    }
    }
    }
