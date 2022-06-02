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
            steps{
               withMaven(mavenSettingsConfig: 'artifactory-maven') {
               sh "mvn clean verify"
               }

            sh 'mvn clean package'
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
   }
}
