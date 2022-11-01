node {
    stage('Checkout') {
        git branch: 'master', url: 'https://github.com/nanelle129/MSD-Auth.git'
    }
    
    stage('Gradle build') {
        sh 'gradle clean build'
    }
    
    stage('Gradle bootJar') {
        sh 'gradle bootJar'
    }
    
     stage ("Containerize the app-docker build - AuthApi") {
        sh 'docker build --rm -t event-auth:v1.0 .'
    }
    
    stage ("Inspect the docker image - AuthApi"){
        sh "docker images event-auth:v1.0"
        sh "docker inspect event-auth:v1.0"
    }
    
    stage ("Run Docker container instance - AuthApi"){
        sh "docker run -d --rm --name event-auth -p 8081:8081 event-auth:v1.0"
    }
    
    stage('User Acceptance Test') {
		def response= input message: 'Is this build good to go?', 
		parameters: [choice(choices: 'Yes\nNo', 
		description: '', name: 'Pass')]
		
		if(response=="Yes") {
	    stage('Deploy to Kubenetes cluster - auth') {
	      sh "kubectl create deployment event-auth --image=event-auth:v1.0"
		sh "set env deployment/event-auth API_HOST=$(kubectl get service/event-data -o jsonpath='{.spec.clusterIP}'):8080"
	      sh "kubectl expose deployment event-auth --type=LoadBalancer --port=8081"
	    }
	  }
	}
	
	stage("Production Deployment View"){
        sh "kubectl get deployments"
        sh "kubectl get pods"
        sh "kubectl get services"
    }
}
