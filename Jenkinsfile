node {
     def app

	environment{
      registry = "jokercat2886/test-jenkins"
      registryCredential = 'DockerHub'
      docker_stop = '\$(docker ps -a -q)'
    }
	
    stage('Clone repository') {
        checkout scm
    }
	
    stage('Build image') {
        app = docker.build("$registry:latest")
    }
	
    stage('Test image') {
        app.inside {
              sh "docker run -p 8001:8000 -d $registry:latest"
	          sh "curl http://127.0.0.1:8001"
	          sh "docker stop $docker_stop"
        }
    }
