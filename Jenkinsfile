node{
	stage('SCM Checkout'){
		git 'https://github.com/itachi-27/my-app.git'
	}
	stage('Compile-Package'){
		def mvnHome =  tool name: 'maven3', type: 'maven'   
			sh "${mvnHome}/bin/mvn clean package"
			sh 'mv target/myweb*.war target/newapp.war'
	}
	stage('SonarQube Analysis'){
		def mvnHome =  tool name: 'maven3', type: 'maven'
		withSonarQubeEnv('sonar') { 
			sh "${mvnHome}/bin/mvn sonar:sonar"
		}
	}   
	stage('Build Docker Imager'){
			sh 'docker build -t itachi2709/myweb:0.0.2 .'
	}
	stage('Docker Image Push'){
		withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
			sh "docker login -u itachi2709 -p ${dockerPassword}"
		}
			sh 'docker push itachi2709/myweb:0.0.2'
	}
	stage('Nexus Image Push'){
		withCredentials([string(credentialsId: 'nexusPass', variable: 'nexusPassword')]) {
			sh "docker login -u admin -p ${nexusPassword} 3.142.245.211:8083"
		}
			sh "docker tag itachi2709/myweb:0.0.2 3.142.245.211:8083/itachi:1.0.0"
			sh 'docker push 3.142.245.211:8083/itachi:1.0.0'
	}   
	stage('Remove Previous Container'){
		try{
			sh 'docker rm -f tomcattest'
		   }
		catch(error){
			// do nothing if there is an exception
		   }
	}
	stage('Docker deployment'){
			sh 'docker run -d -p 8090:8080 --name tomcattest itachi2709/myweb:0.0.2' 
	}
}
