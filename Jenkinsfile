
def SendEmailNotification(String result) {
  
    //config 
    def to = emailextrecipients([
         "shivam.mudotia@nagarro.com,nagender.singh@nagarro.com"
    ])
    
    // set variables
    def subject = "${env.JOB_NAME} - Build #${env.BUILD_NUMBER} ${result}"
    def content = '${JELLY_SCRIPT,template="html"}'

    // send email
    if(to != null && !to.isEmpty()) {
      emailext(body: content, mimeType: 'text/html',
         subject: subject,
         to: to, attachLog: true )
    }
}

pipeline
{
    agent any
    environment {
        PATH = "$PATH:/usr/bin"
        EMAIL_TO = 'shivam.mudotia@nagarro.com'
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
          steps
          {
              withSonarQubeEnv('sonarqube') { 
              sh "mvn sonar:sonar"
              }
          }
       }
     
       
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
                    "target": "sonar-artifactory-sample/"
                  }
                 ]
               }''',
 
            )
         }
       }

    }
    
    post {
       always {
           emailext body: 'Check console output at $BUILD_URL to view the results. \n\n ${CHANGES} \n\n -------------------------------------------------- \n${BUILD_LOG, maxLines=100, escapeHtml=false}', 
           to: "${EMAIL_TO}", 
           subject: '$BUILD_STATUS : $PROJECT_NAME - #$BUILD_NUMBER',
           attachLog: true
       }
    }

}
