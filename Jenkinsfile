pipeline{
     agent any
     Tools{
         maven 'maven'
         jdk 'jdk'
}
  }
  stages {
      stage("Maven Build"){
          steps{
              sh 'mvn -B -DskipTests clean package'
          }
      }
      stage('Maven Test'){
            steps{
                sh 'mvn test'
            }
            post{
            always{
                junit 'target/surefire-reports/*.xml'
            }
        }
        }
     stage("Build & SonarQube analysis") {
            agent any
            steps {
              withSonarQubeEnv('sonarserver') {
                sh 'java -version'
                sh 'mvn clean package sonar:sonar'
              }
            }
          }
     stage("Quality gate") {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }
     stage('Deploy to artifactory'){
        steps{
        rtUpload(
         serverId : 'artifactory-server',
         spec :'''{
           "files" :[
           {
           "pattern":"target/*.jar",
           "target":"art-doc-dev-loc"
           }
           ]
         }''',
         
      )
      }
     }
    }
    post {  
         always {  
             echo 'This will always run'  
         }  
         success {   
            echo "========Deploying executed successfully========"
             mail bcc: '', body: "<b>Example</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> Success!! <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: 'sravaninuthanapati15@gmail.com', mimeType: 'text/html', replyTo: '', subject: "ERROR CI: Project name -> ${env.JOB_NAME}", to: "sravaninuthanapati15@gmail.com"; 
           sshagent(['1076af24-fcf6-4ca0-a2f6-e79e16465615']){
                sh "ssh -o StrictHostKeyChecking=no ubuntu@15.207.88.22  'echo $HOME'"
                    sh 'scp -r /var/jenkins_home/workspace/awsintegration/target/simplecalculator-0.0.1-SNAPSHOT.jar ubuntu@15.207.88.22:/home/ubuntu/artifacts'
         }
         }
         failure {  
             mail bcc: '', body: "<b>Example</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: 'sravaninuthanapati15@gmail.com', mimeType: 'text/html', replyTo: '', subject: "ERROR CI: Project name -> ${env.JOB_NAME}", to: "sravaninuthanapati15@gmail.com";  
         }  
         unstable {  
             echo 'This will run only if the run was marked as unstable'  
         }  
         changed {  
             echo 'This will run only if the state of the Pipeline has changed'  
             echo 'For example, if the Pipeline was previously failing but is now successful'  
         }  
     }
  }
