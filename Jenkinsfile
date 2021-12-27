node{

    def buildNumber = BUILD_NUMBER
    stage("SCM Checkout")   {
      git url: 'https://github.com/vinoyaar/java-web-app-docker.git',branch: 'master'
    }
    
    stage("Maven Clean Package"){
      def mavenHome =  tool name: "Maven", type: "maven"
      sh "${mavenHome}/bin/mvn clean package"
      
    } 
    
    stage("Build Docker Image"){
      sh "docker build -t vjcloud/java-web-app-docker:${buildNumber} ."
    }
    
    stage("Docker Login and Push"){
      withCredentials([string(credentialsId: 'docker_hub_password', variable: 'docker_hub_password')]) {
        sh "docker login -u vjcloud -p ${docker_hub_password}"
      }
      
      sh "docker push vjcloud/java-web-app-docker:${buildNumber}"
    }
     
    stage('Run Docker Image Container in Dev Server'){
        
       sshagent(['docker_ssh_password']) {
          sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.17.122 docker rm -f javawebappcont || true"
 
          sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.17.122 docker run -d -p 8080:8080 --name javawebappcont vjcloud/java-web-app-docker:${buildNumber}"           
          }
       }
 } 
