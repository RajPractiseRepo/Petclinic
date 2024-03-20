pipeline {
    agent any 
    
    tools{
        jdk 'jdk17'
        maven 'maven3'
    }
    
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    
    stages{
        
        stage("Git Checkout"){
            steps{
                git branch: 'main', url: 'https://github.com/RajPractiseRepo/Petclinic.git'
            }
        }
        
        stage("Compile"){
            steps{
                sh "mvn clean compile"
            }
        }
        
         stage("Test Cases"){
            steps{
                sh "mvn test"
            }
        }
        
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Petclinic \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Petclinic '''
    
                }
            }
        }
        
           stage("Build"){
            steps{
                sh " mvn clean package"
            }
        }
        
        stage("OWASP Dependency check"){
            steps{
               dependencyCheck additionalArguments: '--scan target/', odcInstallation: 'owasp'
                  dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
         stage("Artifacts push to Nexus"){
            steps{
               configFileProvider([configFile(fileId: '0613178b-d773-4c9d-80b6-9af1070c613a', variable: 'mavensettings')]) {
                  
                  sh "mvn -s $mavensettings clean deploy -DskipTests=true"
                  
                  
                  
                }
            }
        }
        
         stage("Deploy"){
            steps{
                sh "cp /var/lib/jenkins/workspace/Petclinic-proj@2/target/petclinic.war /opt/apache-tomcat-9.0.65/webapps/ "
            }
        }
        
    }
}
