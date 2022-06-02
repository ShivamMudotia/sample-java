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
                  withMaven(mavenSettingsConfig: 'artifactory-maven') {
                  sh 'mvn deploy:deploy-file -Durl=http://artifactory-ent-devops-1930692006.ap-south-1.elb.amazonaws.com/artifactory/valaxy -DrepositoryId=valaxy -Dfile=in/valaxy/valaxy/2.0-RELEASE/valaxy-2.0-RELEASE.war'

              }
          }
       }
   }
}
