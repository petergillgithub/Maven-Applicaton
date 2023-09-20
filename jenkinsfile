@Library('petersharedlibs') _

pipeline{
    
agent any 

tools {
  maven 'Maven3.9.4'
}

stages{
    
    
stage('CheckOut'){
steps{
sendSlackNotifications('STARTED')
git credentialsId: '715d4479-a4a8-41f2-b03e-c1c53e5a47b8', url: 'https://github.com/petergillgithub/maven-web-application.git'
}
}


stage('BuildPackage'){
steps{
sh "mvn clean package"
}
}


stage('buildImage'){
steps{
sh "docker build -t petergillhmg/maven-web-application:${BUILD_NUMBER} ."

}
}

stage('PushImageDockerHub'){
steps{
withCredentials([string(credentialsId: 'b9f844d7-33fc-41d1-8364-c5d820950d4c', variable: 'Docker_hub')]) {
sh "docker login -u petergillhmg -p ${Docker_hub}"
sh "docker push petergillhmg/maven-web-application:${BUILD_NUMBER}"
}
}
}

/*
stage('Test'){
steps{
sh "mvn test"
}
}


stage('SonarQubeImagesDownload'){
steps{
sh "docker run -d -p 9000:9000 --name sonarqube sonarqube:latest"
}
}



stage('CodeQualityCheck'){
steps{
sh "mvn clean install sonar:sonar"
}
}

stage('NexusArtifact'){
steps{
sshagent(['f6b9edd4-7069-4736-bd51-fe8e5c7cba5c']) {
sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.7.121 docker rm nexusrepo || true"
sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.7.121 docker run -d --name nexusrepo -p 8081:8081 sonatype/nexus3"
}  
}
}

*/

/*
stage('Upload'){
steps{
nexusArtifactUploader artifacts: [
    [
        artifactId: 'maven-web-application', 
        classifier: '', 
        file: 'target/maven-web-application-0.0.1.war', 
        type: 'war'
        ]
        ], 
        credentialsId: 'nexus3', 
        groupId: 'com.mt', 
        nexusUrl: '172.31.7.121', 
        nexusVersion: 'nexus3', 
        protocol: 'http', 
        repository: 'http://18.170.26.121:8081/repository/hdfc-Release/', 
        version: '0.0.1'
}
}

*/

stage('Deployment'){
    steps{
        sshagent(['f6b9edd4-7069-4736-bd51-fe8e5c7cba5c']) {
        sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.19.79 docker rm mavenwebapp || true"
        sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.19.79 docker run -d -p 8080:8080 --name mavenwebapp petergillhmg/maven-web-application:${BUILD_NUMBER}"
            
        }
    }
}


}
post {
  success {
  sendSlackNotifications('currentBuild.result')
  slackSend(color: '#36a64f', message: "Build successful! :white_check_mark:", channel: '#slacknotification')
  }
  failure {
  sendSlackNotifications('currentBuild.result')
  lackSend(color: '#ff0000', message: "Build failed! :x:", channel: '#slacknotification')
  }
}


}
