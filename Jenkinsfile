pipeline {
    agent any

    environment {
        ACR_LOGIN_SERVER = "devopsproject2.azurecr.io"
        IMAGE_NAME = "ekartshopping"
        TAG = "latest"
    }


    stages {

        stage('Checkout'){
	   steps{
	    checkout scm
	   }
	 }
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }


        stage('Building image') {
            steps {
                sh 'docker build -t ekartshopping:latest .'
            }
        }

        stage('Login to ACR') {
       steps {
         withCredentials([usernamePassword(
             credentialsId: 'acr-creds',
             usernameVariable: 'ACR_USER',
             passwordVariable: 'ACR_PASS'
         )]) {
             sh '''
               echo $ACR_PASS | docker login $ACR_LOGIN_SERVER \
               -u $ACR_USER --password-stdin
             '''
         }
       }
     }
	 stage('Tag Image') {
     steps {
        sh '''
          docker tag ${IMAGE_NAME}:${TAG} \
          $ACR_LOGIN_SERVER/${IMAGE_NAME}:${TAG}
        '''
         }
       }
	 stage('Push Image to ACR'){
	   steps{
	    sh 'docker push $ACR_LOGIN_SERVER/${IMAGE_NAME}:${TAG}'
	    }
	   }

        stage('Deploy to k8s cluster'){
	  steps{
	   sh '''
	    kubectl apply -f deploymentservice.yml
		  kubectl apply -f service.yml
      kubectl apply -f ingress.yml
      kubectl apply -f secrets.yml
      kubectl apply -f persvolume.yml
		'''
	   }
	  
	 }


    }
}
