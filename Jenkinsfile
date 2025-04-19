pipeline {
    agent any
    environment {
      MAVEN_OPTS="--add-opens java.base/java.lang=ALL-UNNAMED"   
    }    

    stages {   
        stage('Build with maven') {
            steps {
                sh 'cd SampleWebApp && mvn clean install'
            }
        }
        
             stage('Test') {
            steps {
                sh 'cd SampleWebApp && mvn test'
            }
        
            }
        stage('Code Qualty Scan') {

           steps {
                  withSonarQubeEnv('sonar_scanner') {
 

             sh "mvn -f SampleWebApp/pom.xml sonar:sonar"      
               }
            }
       }
        stage('Quality Gate') {
          steps {
                 waitForQualityGate abortPipeline: true
              }
        }
        
        stage('push to nexus') {
            steps {
              nexusArtifactUploader artifacts: [[artifactId: 'SampleWebApp', classifier: '', file: 'SampleWebApp/target/SampleWebApp.war', type: 'war']], credentialsId: 'nexuspass', groupId: 'SampleWebApp', nexusUrl: 'ec2-13-217-88-160.compute-1.amazonaws.com:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'test_repo', version: '1.0-SNAPSHOT'
            
        }
        }    
        stage('deploy to tomcat') {
          steps {
             deploy adapters: [tomcat9(credentialsId: '3db54c88-db5a-4ee0-b8ef-3ec54fb1ff4e', path: '', url: 'http://54.225.48.26:8080/')], contextPath: 'myapp', war: '**/*.war'
                          
              
          }
            
        }
            
        }
    }
