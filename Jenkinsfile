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
                  curl -uadmin:AP4jna4unj3rj6cU4sY1bxQ3Hanuwjc1wj8S22 -T target/valaxy-2.0-RELEASE.war "http://artifactory-ent-devops-1930692006.ap-south-1.elb.amazonaws.com/artifactory/valaxy/valaxy-2.0-RELEASE.war"
          }
       }
   }
}
