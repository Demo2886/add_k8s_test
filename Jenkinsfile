//------------------------------------------
// Pipeline Workflow
//
// Version      Date        Info
// 1.∞          2022        Initial Version
//
// Made by Alexandr Nefedin (c) 2022
//-------------------------------------------

node {
     def app
	
    stage('Clone repository') {
        //git url:'https://github.com/Demo2886/add_k8s_test.git', branch:'master'
        checkout scm
    }
 
     stage('Test Dockerfile hadolint') {

            sh "docker run --rm -i hadolint/hadolint hadolint --ignore DL3013  --ignore DL3042  - < Dockerfile"
  
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
        sh "docker push jokercat2886/test-jenkins:latest"		
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
              slackSend (color: '#FF0000', message: "Deployment failed: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'")
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
                    slackSend (color: '#00FF00', message: "Deployment success: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'")
              }
            }
            catch(Exception err){
              throw new Exception(slackSend (color: '#FF0000', message: "Deployment failed: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"))
              //error "Deployment filed"
              //slackSend (color: '#FF0000', message: "Deployment failed: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'")
            }
        
        }
      }
      
    }

    

          
}	
	
	
	
