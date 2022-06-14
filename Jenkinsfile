
//def SendEmailNotification(String result) {
  
    // config 
    def to = emailextrecipients([
         "shivam.mudotia@nagarro.com"
    ])
    
    // set variables
    def subject = "${env.JOB_NAME} - Build #${env.BUILD_NUMBER} ${result}"
    def content = '${JELLY_SCRIPT,template="html"}'

    // send email
   // if(to != null && !to.isEmpty()) {
   //   emailext(body: content, mimeType: 'text/html',
   //      subject: subject,
   //      to: to, attachLog: true )
    //}
//}

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

    }

    post {
     always {
            emailext to: to,
            subject: subject,
            body: "${currentBuild.currentResult}: Job ${env.JOB_NAME}\nMore Info can be found here: ${env.BUILD_URL}",
            attachLog: true
     }
//      success {
//             emailext to: to,
//             subject: subject,
//             body: "${currentBuild.currentResult}: Job ${env.JOB_NAME}\nMore Info can be found here: ${env.BUILD_URL}",
//             attachLog: true
//      }

//     failure{
//             emailext to: to,
//             subject: subject,
//             body: "${currentBuild.currentResult}: Job ${env.JOB_NAME}\nMore Info can be found here: ${env.BUILD_URL}",
//             attachLog: true
//         }
//      unstable {
//             emailext to: to,
//             subject: subject,
//             body: "${currentBuild.currentResult}: Job ${env.JOB_NAME}\nMore Info can be found here: ${env.BUILD_URL}",
//             attachLog: true
//      }
//     changed{
//             emailext to: to,
//             subject: subject,
//             body: "${currentBuild.currentResult}: Job ${env.JOB_NAME}\nMore Info can be found here: ${env.BUILD_URL}",
//             attachLog: true
//  }
}
}
