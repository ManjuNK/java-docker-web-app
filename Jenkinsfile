node{
  environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        }
     
    stage('SCM Checkout'){
        git url: 'https://github.com/ManjuNK/java-docker-web-app.git',branch: 'main'
    }
    
    stage(" Maven Clean Package"){
      def mavenHome =  tool name: "Maven-3.8.6", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
      
    } 
    
    stage('Build Docker Image'){
      sh 'docker build -t manjunk/java-docker-web-app .'
        }
    
    stage('Push Docker Image'){
      withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) 
      {
        sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
        
        sh 'docker push manjunk/java-docker-web-app'
            }
     
      stage('Run Docker Image In Dev Server'){
        
        def dockerRun = ' docker run  -d -p 6060:6060 --name java-docker-web-app manjunk/java-docker-web-app'
         
         sshagent(['Tomcat-Server_Agent']) {
          sh 'ssh -o StrictHostKeyChecking=no ubuntu@3.64.237.2 docker stop java-docker-web-app || true'
          sh 'ssh  ubuntu@3.64.237.2 docker rm java-docker-web-app || true'
          sh 'ssh  ubuntu@3.64.237.2 docker rmi -f  $(docker images -q) || true'
          sh "ssh  ubuntu@3.64.237.2 ${dockerRun}"
       }
       
    }
}
}