pipeline {
    agent any

    stages {
        
        stage('Git Checkout') {
            
            steps {
               
               script {
                   git branch: 'main', url: 'https://github.com/Raghava0684/Jenkins-Nexus-Project.git'
               }
            }
        }
        
        stage ('Unit Testing') {
            
            steps {
                
                script {
                    sh 'mvn test'
                }
            }
        }
        
        stage ('Integration Testing') {
            
            steps {
                
                script {
                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }
        
        stage ('maven Build') {
            
            steps {
                
                script {
                    sh 'mvn clean install'
                }
            }
        }
        
        stage ('Static Code Analysis') {
            
            steps {
                
                script {
                
                withSonarQubeEnv(credentialsId: 'Sonarqube-Secret') {
            
                 sh 'mvn clean package sonar:sonar'
                }
            }
            
            }
        }
        
        stage ('Quality Gate status') {
            
            steps {
                
                script {
                    
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonarqube-Secret'
                }
            }
        }
        
        stage ('upoad war file  in to nexus') {
            
            
            steps {
                
                script {
                   
                   def readPomVersion = readMavenPom file: 'pom.xml'
                   
                   
                   nexusArtifactUploader artifacts:
                   [
                       [
                           artifactId: 'springboot', 
                           classifier: '', file: 'target/Uber.jar', 
                           type: 'jar'
                        ]
                    ], 
                    credentialsId: 'Nexus-Credentials', 
                    groupId: 'com.example', 
                    nexusUrl: '54.183.200.89:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: "java-app-release", 
                    version: "${readPomVersion.version}"
                }
            }
        }

        stage ('Docker image build') {

            steps {

                script {
                    sh 'docker  build -t 9642908704 .'
                    sh 'docker login -u 9642908704 -p 168B5A0302'
                    sh 'docker push 9642908704:latest'
                }
            }
        }
        
}

}
