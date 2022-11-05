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
        sh 'docker push jokercat2886/test-jenkins:latest'		
		sh "docker stop \$(docker ps -a -q)"
      }
      
    stage('Apply Kubernetes files') {
      withKubeConfig([credentialsId: 'kubernetes']) {
        sh 'kubectl get namespace | grep -q "^pre-prod " || kubectl create namespace pre-prod'
        sh 'kubectl apply -f k8s_bom.yaml --namespace=pre-prod'
        sleep 4
        sh 'kubectl get pods --namespace=pre-prod'
      }
    }
    
    
    stage('Deploy in prod') {
      script {
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
          def depl = true
            try{
              input("Deploy in prod?")
            }
            catch(err){
              depl = false
            }
            try{
              if(depl){
              withKubeConfig([credentialsId: 'kubernetes']) {
                sh 'kubectl get namespace | grep -q "^prod " || kubectl create namespace prod'
                sh 'kubectl apply -f k8s_bom.yaml'
                sleep 4
                sh 'kubectl get pods --namespace=prod'
                sh 'kubectl delete -f k8s_bom.yaml --namespace=pre-prod'
                sh 'kubectl delete namespace pre-prod'
              }
              }
            }
            catch(Exception err){
              error "Deployment filed"
            }
        
        }
      }
      
    }	
}	
	
	
	
