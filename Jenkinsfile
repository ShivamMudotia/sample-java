

def SendEmailNotification(String result) {
  
    if(to != null && !to.isEmpty()) {
      emailext(body: body, mimeType: 'text/html',
         subject: subject,
         to: to, attachLog: true )
    }
}

pipeline
{
    agent any
    environment {
        PATH = "$PATH:/usr/bin"
        email_to = "shivam.mudotia@nagarro.com"
    }
    
    stages
    {

       stage('Build')
       {
            steps
            {
              withMaven(mavenSettingsConfig: 'artifactory-maven') {
                  sh "mvn clean verify"
                  sh 'mvn clean package'
                }
            }    
       }
        

       stage('SonarQube analysis') 
       {
          steps{
              withSonarQubeEnv('sonarqube') { 
              sh "mvn sonar:sonar"
              }
              script {
                  timeout(time: 5, unit: 'MINUTES') {
                      def qualitygate = waitForQualityGate()
                      if (qualitygate.status != "OK") {
                         error "Pipeline aborted due to Quality Gate Failure."
                         }
                      }
                  }
              }
       }

    // This is only available with Artifactory Commercial License 
    //    stage ('Set build retention') 
    //    {
    //      steps {
    //         rtBuildInfo (
    //             maxBuilds: 10
    //         )
    //       }   
    //    }
    

       stage('Push Artifacts to Artifactory')
       {
          steps
          {

              rtUpload (
                 serverId: 'artifactory',
                 spec: '''{
                    "files": [
                    {
                      "pattern": "*.war",
                      "target": "app1/sonar-artifactory-sample-1/"
                    }
                   ]
                 }''',
              )
            }
       }  
       
       stage ('Publish build info') 
       {
           steps 
           {
              rtPublishBuildInfo (
                 serverId: 'artifactory'
              )
           }
        }
         
    }
    
    
    post {
       always {
           emailext body: 'Check detailed console output at below URL. \n $BUILD_URL \n\n Code Commits \n\n ${CHANGES} \n\n -------------------------------------------------- \n\n Build Logs (Truncated) - Full Logs Attached \n\n${BUILD_LOG, maxLines=100, escapeHtml=false}', 
           to: "${email_to}", 
           subject: '$BUILD_STATUS : $PROJECT_NAME - #$BUILD_NUMBER',
           attachLog: true
       }
    }

}

