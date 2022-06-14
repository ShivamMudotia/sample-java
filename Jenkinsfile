
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


       stage('post-build') 
       {
        steps{
       SendEmailNotification("SUCCESSFUL")
        }
       }

    }

    post {
     always {
      sh 'echo "This will always run"'
     }
     success {
      sh 'echo "This will run only if successful"'
     }
     failure {
      sh 'echo "This will run only if failed"'
     }
     unstable {
      sh 'echo "This will run only if the run was marked as unstable"'
     }
     changed {
      sh 'echo "This will run only if the state of the Pipeline has changed"'
      sh 'echo "For example, the Pipeline was previously failing but is now successful"'
      sh 'echo "... or the other way around :)"'
     }
   
 }
}
