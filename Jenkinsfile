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
                
                withSonarQubeEnv(credentialsId: 'sonarqube-token2') {
            
                 sh 'mvn clean package sonar:sonar'
                }
            }
            
            }
        }
        
        stage ('Quality Gate status') {
            
            steps {
                
                script {
                    
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube-token2'
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
                    credentialsId: 'Nexus-Credentials-2',
                    groupId: 'com.example', 
                    nexusUrl: '13.57.217.6:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: 'jenkins-nexus-project',
                    version: "${readPomVersion.version}"
                }
            }
        }

        stage ('Docker image build') {

            steps {

                script {

                    sh 'docker  build -t raghav0617/jenkinsnexusimage .'

                }
            }
        }

        stage ('Push Image to Docker-hub') {

            steps {
                
                script {

                    sh 'docker login -u raghav0617 -p 168B5A0302'
                    sh 'docker push raghav0617/jenkinsnexusimage:latest'

                }
            }
        }
        
}

}
