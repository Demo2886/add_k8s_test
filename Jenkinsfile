node {
     def app

	environment{
//      registry = "jokercat2886/test-jenkins"
      registryCredential = 'DockerHub'
//      docker_stop = '\$(docker ps -a -q)'
   }
	
    stage('Clone repository') {
        checkout scm
    }
	
    stage('Build image') {
        app = docker.build("jokercat2886/test-jenkins")
    }

     stage('Build run') {
            sh "docker run -p 8001:8000 -d jokercat2886/test-jenkins:latest"
    }
     stage('docker test') {
            sh "curl http://127.0.0.1:8001"
    }

    stage('Push Image to repo') {
        sh 'docker push $registryCredential/test-jenkins:latest'		
		sh "docker stop \$(docker ps -a -q)"
      }
	
}	
	
	
	
