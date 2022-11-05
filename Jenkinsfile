node {
     def app

//	environment{
//      registry = "jokercat2886/test-jenkins"
//      registryCredential = 'DockerHub'
//      docker_stop = '\$(docker ps -a -q)'
//    }
	
    stage('Clone repository') {
        checkout scm
    }
	
    stage('Build image') {
        app = docker.build("jokercat2886/test-jenkins")
    }
	
//    stage('Test image') {
//        app.inside {
//            sh "docker run -p 8001:8000 -d jokercat2886/test-jenkins:latest"
//	          sh "curl http://127.0.0.1:8001"
//	          sh "docker stop \$(docker ps -a -q)"
//        }
//    }
	
    environment {
        DOCKER_ID = credentials('DockerHub')
        DOCKER_PASSWORD = credentials('jokercat2886')
    }

    stages {
        stage('Init') {
            steps {
                echo 'Initializing..'
                echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
                echo "Current branch: ${env.BRANCH_NAME}"
                sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_ID --password-stdin'
            }
        }
        stage('Build') {
            steps {
                echo 'Building image..'
                sh 'docker build -t $DOCKER_ID/test-jenkins:latest .'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
                sh "curl http://127.0.0.1:8001"
				//sh 'docker run --rm -e CI=true $DOCKER_ID/test-jenkins pytest'
            }
        }
        stage('Publish') {
            steps {
                echo 'Publishing image to DockerHub..'
                sh 'docker push $DOCKER_ID/test-jenkins:latest'
            }
        }
        stage('Cleanup') {
            steps {
                echo 'Removing unused docker containers and images..'
                sh 'docker ps -aq | xargs --no-run-if-empty docker rm'
                // keep intermediate images as cache, only delete the final image
                sh 'docker images -q | xargs --no-run-if-empty docker rmi'
            }
        }
    }

	
}	
	
	
	
