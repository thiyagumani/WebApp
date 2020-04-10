pipeline {
     agent any
     stages {
         stage('Build') {
             steps {
                 echo 'Building...'
             }
             post {
                 always {
                     jiraSendBuildInfo site: 'devopsgroups2.atlassian.net'
                 }
             }
         }
     }
 }
