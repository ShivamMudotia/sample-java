
def SendEmailNotification(String result) {
  
    // config 
    def to = emailextrecipients([
         "shivam.mudotia@nagarro.com"
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
    }
    
    stages
    {

       try 
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
      }
        
       try 
       {
       stage('SonarQube analysis') 
       {
          steps
          {
              withSonarQubeEnv('sonarqube') { 
              sh "mvn sonar:sonar"
              }
          }
       }
       }
     
       try 
       {
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


       try 
       {
        stage('post-build') 
       {
        steps{
       SendEmailNotification("SUCCESSFUL")
        }
       }
       }

       catch(e) {
    // mark build as failed
    currentBuild.result = "FAILURE";
   
    SendEmailNotification(currentBuild.result)

    // mark current build as a failure and throw the error
    throw e;
     }

    }


}


