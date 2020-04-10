pipeline {
    agent any
    stages {
        stage('SCM') {
            steps {
                git url: 'https://github.com/midhunthampi/WebApp.git'
            }
        }
        stage('build && SonarQube analysis') {
            steps {
                withSonarQubeEnv(credentialsId: 'sonarqube1', installationName: 'sonarqube1') {
                    // Optionally use a Maven environment you've configured already
                    withMaven(maven:'maven') {
                        sh 'mvn clean package sonar:sonar'
                    }
                }
            }
        }
        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
