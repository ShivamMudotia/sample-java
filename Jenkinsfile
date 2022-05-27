pipeline
{
    agent any
    environment {
        PATH = "$PATH:/usr/bin"
    }
    
    stages
    {
       stage('GetCode')
       {
            steps{
                git 'https://github.com/ShivamMudotia/sample-java.git'
            }
       }        
       
       stage('Build')
       {
            steps{
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
